---
title: 设置群集见证
description: 了解如何设置群集见证
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 32d0f717e987d757f5315cfe048c75300ae9c776
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647881"
---
# <a name="set-up-a-cluster-witness"></a>设置群集见证

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

强烈建议为所有群集设置见证资源，并应在创建群集后立即设置见证资源。 双节点群集需要见证，这样其中任一服务器脱机就不会导致另一个节点不可用。 三个及更多节点的群集需要见证，才能承受两台服务器故障或脱机。  

你可以使用 SMB 文件共享作为见证服务器或 Azure 云见证服务器。 建议使用 Azure 云见证，前提是群集中的所有服务器节点都具有可靠的 Internet 连接。 本文介绍如何创建云见证。

## <a name="before-you-begin"></a>在开始之前

你必须具有 Azure 帐户和订阅，并将 Azure Stack HCI 群集注册到 Azure，然后才能创建云见证。 有关详细信息，请参阅以下文章：

- [创建 Azure 帐户](https://docs.microsoft.com/dotnet/azure/create-azure-account)
- 如果适用，请 [创建其他 Azure 订阅](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)
- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)

对于文件共享见证服务器，有文件服务器要求。 有关详细信息，请参阅[系统需求](../concepts/system-requirements.md)。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

本部分介绍如何创建 Azure 存储帐户。 此帐户用于存储用于特定群集的仲裁的 Azure blob 文件。 可以使用相同的 Azure 存储帐户来配置多个群集的云见证。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 "Azure 门户主菜单上的" **Azure 服务**"下，选择" **存储帐户**"。 如果缺少此图标，请选择 " **创建资源** " 以首先创建 *存储帐户* 资源。

    :::image type="content" source="media/witness/cloud-witness-home.png" alt-text="Azure 门户主屏幕" lightbox="media/witness/cloud-witness-home.png":::

1. 在 " **存储帐户** " 页上，选择 " **新建**"。

    :::image type="content" source="media/witness/cloud-witness-create.png" alt-text="Azure 新存储帐户" lightbox="media/witness/cloud-witness-create.png":::

1. 在 " **创建存储帐户** " 页上，完成以下操作：
    1. 选择要将存储帐户应用到的 Azure **订阅** 。
    1. 选择要将存储帐户应用到的 Azure **资源组** 。
    1. 输入“存储帐户名称”。
    <br>存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 此名称在 Azure 中也必须是唯一的。
    1. 选择最靠近你的 **位置** 。
    1. 对于“性能”，请选择“标准”。
    1. 对于 " **帐户类型**"，请选择 " **存储常规用途**"。
    1. 对于 **复制**，请选择 " **本地冗余存储 (LRS)**"。
    1. 完成后，单击 " **审核 + 创建**"。

    :::image type="content" source="media/witness/cloud-witness-create-storage-account.png" alt-text="Azure 创建存储帐户" lightbox="media/witness/cloud-witness-create-storage-account.png":::

1. 请确保存储帐户通过验证，然后查看帐户设置。 完成后，单击“创建”。

    :::image type="content" source="media/witness/cloud-witness-validation.png" alt-text="Azure 存储帐户验证" lightbox="media/witness/cloud-witness-validation.png":::

1. 在 Azure 中执行帐户部署可能需要几秒钟时间。 部署完成后，单击 " **前往资源**"。

    :::image type="content" source="media/witness/cloud-witness-deployment.png" alt-text="Azure 存储帐户部署" lightbox="media/witness/cloud-witness-deployment.png":::

## <a name="copy-the-access-key-and-endpoint-url"></a>复制访问密钥和终结点 URL

创建 Azure 存储帐户时，该过程会自动生成两个访问密钥，主键 (key1) ，辅助密钥 (key2) 。 首次创建云见证时，将使用 **key1** 。 还会自动生成终结点 URL。

Azure 云见证使用 blob 文件进行存储，其终结点生成的端点为形式 *storage_account_name* 作为终结点。 

> [!NOTE]  
> Azure 云见证使用 HTTPS (默认端口 443) 来建立与 Azure blob 服务的通信。 确保可以访问 HTTPS 端口。

### <a name="copy-the-account-name-and-access-key"></a>复制帐户名称和访问密钥

1. 在 Azure 门户的 " **设置**" 下，选择 " **访问密钥**"。
1. 选择 " **显示密钥** " 以显示关键信息。
1. 单击 **存储帐户名称** 和 **key1** 字段右侧的 "复制并粘贴" 图标，将每个文本字符串粘贴到记事本或其他文本编辑器。

    :::image type="content" source="media/witness/cloud-witness-access-keys.png" alt-text="Azure 存储帐户访问密钥" lightbox="media/witness/cloud-witness-access-keys.png":::

### <a name="copy-the-endpoint-url-optional"></a>将终结点 URL 复制 (可选) 

此终结点 URL 是可选的，云见证可能不需要该 URL。

1. 在 Azure 门户中，选择 " **属性**"。
1. 选择 " **显示密钥** " 以显示终结点信息。
1. 在 " **blob 服务**" 下，单击 " **blob 服务** " 字段右侧的 "复制并粘贴" 图标，然后将文本字符串粘贴到记事本或其他文本编辑器。

    :::image type="content" source="media/witness/cloud-witness-blob-service.png" alt-text="Azure blob 终结点" lightbox="media/witness/cloud-witness-blob-service.png":::

## <a name="create-a-cloud-witness-using-windows-admin-center"></a>使用 Windows 管理中心创建云见证

现在，你已准备好使用 Windows 管理中心为群集创建见证服务器实例。

1. 在 Windows Admin Center 中，从顶部下拉箭头中选择“群集管理器”。
1. 在“群集连接”下，选择该群集。
1. 在“工具”下，选择“设置” 。
1. 在右窗格中，选择“见证”。
1. 对于“见证类型”，请选择以下选项之一：
      - **云见证** -按前面所述输入 Azure 存储帐户名称、访问密钥和终结点 URL
      - **文件共享见证** - 输入文件共享路径“(//server/share)”
1. 对于云见证，为以下字段粘贴前面复制的文本字符串：
    1. **Azure 存储帐户名称**
    1. **Azure 存储访问密钥**
    1. **Azure 服务终结点**

    :::image type="content" source="media/witness/cloud-witness-1.png" alt-text="云见证访问密钥" lightbox="media/witness/cloud-witness-1.png":::

1. 完成后，单击“保存”。 信息可能需要一些时间才能传播到 Azure。

> [!NOTE]
> 第三个选项 **磁盘见证** 不适用于延伸群集。

## <a name="create-a-cloud-witness-using-windows-powershell"></a>使用 Windows PowerShell 创建云见证

或者，可以使用 PowerShell 为群集创建见证服务器实例。

使用以下 cmdlet 创建 Azure 云见证。 如前文所述，输入 Azure 存储帐户名称和访问密钥信息：

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

使用以下 cmdlet 创建文件共享见证。 输入文件服务器共享的路径：

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>后续步骤

- 有关群集仲裁的详细信息，请参阅[了解 Azure Stack HCI 上的群集和池仲裁](../concepts/quorum.md)。

- 有关创建和管理 Azure 存储帐户的详细信息，请参阅 [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create)。