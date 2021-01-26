---
title: 设置群集见证
description: 了解如何设置群集见证
author: v-dasis
ms.topic: how-to
ms.date: 01/21/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: cb964bafae7dd9252b386c30a12251e89fc8ead5
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98781940"
---
# <a name="set-up-a-cluster-witness"></a>设置群集见证

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

所有群集都必须设置见证资源，并且应在创建群集后立即设置。 双节点群集需要见证，这样其中任一服务器脱机就不会导致另一个节点不可用。 三个及更多节点的群集需要见证，才能承受两台服务器故障或脱机。  

可以使用 SMB 文件共享作为见证或使用 Azure 云见证。 建议使用 Azure 云见证，前提是群集中的所有服务器节点都具有可靠的 Internet 连接。 有关详细信息，请参阅[部署故障转移群集的云见证](/windows-server/failover-clustering/deploy-cloud-witness)。

文件共享见证对文件服务器有要求。 有关详细信息，请参阅[系统需求](../concepts/system-requirements.md)。

## <a name="set-up-a-witness-using-windows-admin-center"></a>使用 Windows Admin Center 设置见证

1. 在 Windows Admin Center 中，从顶部下拉箭头中选择“群集管理器”。
1. 在“群集连接”下，选择该群集。
1. 在“工具”下，选择“设置” 。
1. 在右窗格中，选择“见证”。
1. 对于“见证类型”，请选择以下选项之一：
      - **云见证** - 输入 Azure 存储帐户名称、访问密钥和终结点 URL，如下所述
      - **文件共享见证** - 输入文件共享路径“(//server/share)”

> [!NOTE]
> 第三个选项 **磁盘见证** 不适用于延伸群集。

## <a name="create-an-azure-storage-account-to-use-as-a-cloud-witness"></a>创建要用作云见证的 Azure 存储帐户

本部分介绍如何创建存储帐户并查看和复制该帐户的终结点 URL 和访问密钥。

若要配置云见证，你必须有一个有效的 Azure 存储帐户，该帐户可用于存储 blob 文件（用于仲裁）。 Cloud 见证在 Microsoft 存储帐户下创建一个众所周知的容器 **msft-见证** 服务器。 云见证写入一个 blob 文件，其中包含对应群集的唯一 ID，该 ID 用作此 msft-cloud-witness 容器下 blob 文件的文件名。 这意味着，你可以使用同一个 Microsoft Azure 存储帐户为多个不同的群集配置云见证。

使用相同的 Azure 存储帐户为多个不同的群集配置云见证时，会自动创建一个 msft-cloud-witness 容器。 此容器将为每个群集包含一个 blob 文件。

> [!NOTE]  
> 云见证使用 HTTPS（默认端口 443）来与 Azure blob 服务建立通信。 确保可通过网络代理访问 HTTPS 端口。

### <a name="to-create-an-azure-storage-account"></a>创建 Azure 存储帐户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“中心”菜单上，选择“新建”->“数据 + 存储”->“存储帐户”。
1. 在“创建存储帐户”页中执行以下操作：
    1. 为存储帐户输入名称。
    <br>存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 存储帐户名称在 Azure 中也必须是唯一的。
    1. 对于“帐户类型”，选择“常规用途” 。
    <br>不能将 Blob 存储帐户用于云见证。
    1. 对于“性能”，请选择“标准”。
    <br>不能将 Azure 高级存储用于云见证。
    1. 对于“复制”，选择“本地冗余存储(LRS)” 。
    <br>故障转移群集使用 blob 文件作为仲裁点，这在读取数据时需要一些一致性保证。 因此“副本”类型必须选择“本地冗余存储” 。

### <a name="view-and-copy-storage-access-keys-for-your-azure-storage-account"></a>查看和复制 Azure 存储帐户的存储访问密钥

创建 Microsoft Azure 存储帐户时，它会与自动生成的两个访问密钥关联-主访问密钥和辅助访问密钥。 首次创建云见证时，请使用主访问密钥。 对于要用于云见证的密钥没有任何限制。  

#### <a name="to-view-and-copy-storage-access-keys"></a>查看和复制存储访问密钥

在 Azure 门户中，导航到你的存储帐户，单击“所有设置”，然后单击“配置”以查看、复制和再生成帐户访问密钥 。 “访问密钥”边栏选项卡还包含使用主密钥和辅助密钥预配置的连接字符串，可复制到应用程序中使用。

:::image type="content" source="media/witness/cloud-witness-1.png" alt-text="云见证访问密钥" lightbox="media/witness/cloud-witness-1.png":::

### <a name="view-and-copy-endpoint-url-links"></a>查看和复制终结点 URL 链接

创建存储帐户时，将使用此格式生成以下 URL：`https://<Storage Account Name>.<Storage Type>.<Endpoint>`  

云见证始终使用 Blob 作为存储类型。 Azure 使用 **core.windows.net** 作为终结点。 配置云见证时，可以根据方案使用不同的终结点进行配置， (例如，中国的 Microsoft Azure 数据中心具有不同的终结点) 。  

> [!NOTE]  
> 此终结点 URL 由云见证资源自动生成，并且该 URL 无需额外的配置步骤。  

#### <a name="to-view-and-copy-endpoint-url-links"></a>查看和复制终结点 URL 链接

在 Azure 门户中，导航到你的存储帐户，单击“所有设置”，然后单击“属性”以查看和复制终结点 URL 。  

:::image type="content" source="media/witness/cloud-witness-2.png" alt-text="云见证终结点 URL" lightbox="media/witness/cloud-witness-2.png":::  

## <a name="set-up-a-witness-using-windows-powershell"></a>使用 Windows PowerShell 设置见证

若要使用 PowerShell 设置群集见证，请运行以下 cmdlet 之一。

使用以下 cmdlet 创建 Azure 云见证：

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

使用以下 cmdlet 创建文件共享见证：

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>后续步骤

- 有关群集仲裁的详细信息，请参阅[了解 Azure Stack HCI 上的群集和池仲裁](../concepts/quorum.md)。

- 有关创建和管理 Azure 存储帐户的详细信息，请参阅[关于 Azure 存储帐户](/azure/storage/common/storage-account-create)。