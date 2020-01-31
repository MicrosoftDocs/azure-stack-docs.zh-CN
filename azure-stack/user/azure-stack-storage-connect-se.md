---
title: 将存储资源管理器连接到 Azure Stack 中心订阅或存储帐户
description: 了解如何将存储资源管理器连接到 Azure Stack 中心订阅
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 2776443fac5e30b14154e96618a49bd89d16a7c0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884396"
---
# <a name="connect-storage-explorer-to-an-azure-stack-hub-subscription-or-a-storage-account"></a>将存储资源管理器连接到 Azure Stack 中心订阅或存储帐户

本文介绍如何使用[Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)连接到 Azure Stack 中心订阅和存储帐户。 存储资源管理器是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure Stack 中心存储数据。

> [!NOTE]  
> 有多种工具可用于将数据移入和移出 Azure Stack 中心存储。 有关详细信息，请参阅[Azure Stack 中心存储的数据传输工具](azure-stack-storage-transfer.md)。

如果尚未安装，请[下载](https://www.storageexplorer.com/)并安装存储资源管理器。

连接到 Azure Stack 中心订阅或存储帐户后，可以使用[Azure 存储资源管理器文章](/azure/vs-azure-tools-storage-manage-with-storage-explorer)来处理 Azure Stack 中心数据。 

## <a name="prepare-for-connecting-to-azure-stack-hub"></a>准备连接到 Azure Stack 集线器

需要直接访问 Azure Stack 集线器或 VPN 连接，存储资源管理器才能访问 Azure Stack 中心订阅。 若要了解如何设置与 Azure Stack 集线器的 VPN 连接，请参阅[使用 VPN 连接到 Azure Stack 集线器](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)。

> [!Note]  
> 对于 ASDK，如果要通过 VPN 连接到 ASDK，请不要使用在 VPN 安装过程中创建的根证书（）。  这是 DER 编码的证书，不允许存储资源管理器检索 Azure Stack 中心订阅。 使用以下步骤导出要与存储资源管理器一起使用的64编码证书。

对于断开连接和 ASDK 的集成系统，建议使用内部企业证书颁发机构以64格式导出根证书，然后将其导入 Azure 存储资源管理器。  

### <a name="export-and-then-import-the-azure-stack-hub-certificate"></a>导出并导入 Azure Stack 中心证书

导出并导入已断开连接的集成系统和 ASDK 的 Azure Stack 集线器证书。 对于连接的集成系统，证书已公开签名，无需执行此步骤。

1. 使用连接到 Azure Stack 中心的 VPN 连接打开 Azure Stack 中心主机或本地计算机上的 `mmc.exe`。 

2. 在 "**文件**" 中，选择 "**添加/删除管理单元**"。 在 "可用的管理单元" 中选择**证书**。 

3. 选择“计算机帐户”，然后选择“下一步”。 选择 "**本地计算机**"，然后选择 "**完成**"。

4.  在**Console Root\Certificated （本地计算机） \Trusted Root 认证机构 \** find **AzureStackSelfSignedRootCert**。

    ![通过 mmc.exe 加载 Azure Stack 中心根证书](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. 右键单击该证书，选择 "**所有任务**" > "**导出**"，然后按照说明将证书导出为**64 编码的 x.509 （。CER）** 。

    导出的证书会在下一步使用。

6. 开始存储资源管理器。 如果看到 "**连接到 Azure 存储**" 对话框，请将其取消。

7. 在 "**编辑**" 菜单上，选择 " **SSL 证书**"，然后选择 "**导入证书**"。 通过文件选取器对话框找到并打开在上一步导出的证书。

    导入证书后，系统会提示重新启动存储资源管理器。

    ![将证书导入存储资源管理器](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. 存储资源管理器重新启动后，选择 "**编辑**" 菜单，查看是否已选择 "**目标 Azure Stack 中心 api** "。 如果没有，请选择 "**目标 Azure Stack 中心**"，然后重新启动存储资源管理器以使更改生效。 与 Azure Stack 中心环境兼容需要此配置。

    ![确保选择 "目标 Azure Stack 集线器"](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-azure-ad"></a>使用 Azure AD 连接到 Azure Stack 中心订阅

使用以下步骤将存储资源管理器连接到属于 Azure Active Directory （Azure AD）帐户的 Azure Stack 中心订阅。

1. 在存储资源管理器的左窗格中，选择 "**管理帐户**"。  
    将显示你登录的所有 Microsoft 订阅。

2. 若要连接到 Azure Stack 中心订阅，请选择 "**添加帐户**"。

    ![添加 Azure Stack 中心帐户](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. 在 "连接到 Azure 存储" 对话框中的 " **azure 环境**" 下，选择 " **Azure**、 **azure 中国世纪互联**、 **azure 德国**、 **azure 美国政府**" 或 "**添加新环境**"。 这取决于所使用的 Azure Stack 中心帐户。 选择 "**登录**" 以与至少一个活动 Azure Stack 中心订阅关联的 Azure Stack 中心帐户登录。

    ![连接到 Azure 存储](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. 使用 Azure Stack 中心帐户成功登录后，左窗格将填充与该帐户关联的 Azure Stack 中心订阅。 选择要使用的 Azure Stack 中心订阅，并选择 "**应用**"。 （如果选中或清除 "**所有订阅**" 复选框，则会在所有列出的 Azure Stack 中心订阅中进行选择。）

    ![填充 "自定义云环境" 对话框后，选择 "Azure Stack 中心订阅"](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    左窗格显示与所选 Azure Stack 中心订阅关联的存储帐户。

    ![存储帐户列表，包括 Azure Stack 中心订阅帐户](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-ad-fs-account"></a>使用 AD FS 帐户连接到 Azure Stack 中心订阅

> [!Note]  
> Azure 联合身份验证服务（AD FS）登录体验支持 Azure Stack Hub 1804 或更新版本的存储资源管理器1.2.0 或更高版本。
使用以下步骤将存储资源管理器连接到属于 AD FS 帐户的 Azure Stack 中心订阅。

1. 选择 "**管理帐户**"。 资源管理器将列出你登录到的 Microsoft 订阅。
2. 选择 "**添加帐户**" 以连接到 Azure Stack 中心订阅。

    ![添加帐户-存储资源管理器](media/azure-stack-storage-connect-se/add-an-account.png)

3. 选择“**下一页**”。 在 "连接到 Azure 存储" 对话框中的 " **azure 环境**" 下，选择 "**使用自定义环境**"，然后单击 "**下一步**"。

    ![连接到 Azure 存储](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. 输入 Azure Stack 中心自定义环境所需的信息。 

    | 字段 | 说明 |
    | ---   | ---   |
    | 环境名称 | 用户可以自定义该字段。 |
    | Azure 资源管理器终结点 | Azure Stack 开发工具包的 Azure 资源管理器资源终结点的示例。<br>对于运算符： https://adminmanagement.local.azurestack.external <br> 对于用户： https://management.local.azurestack.external |

    如果你正在 Azure Stack 集线器集成系统，但不知道管理终结点，请与你的操作员联系。

    ![添加帐户自定义环境](./media/azure-stack-storage-connect-se/custom-environments.png)

5. 选择 "**登录**" 以连接到与至少一个活动 Azure Stack 中心订阅关联的 Azure Stack 中心帐户。



6. 选择要使用的 Azure Stack 中心订阅，并选择 "**应用**"。

    ![帐户管理](./media/azure-stack-storage-connect-se/account-management.png)

    左窗格显示与所选 Azure Stack 中心订阅关联的存储帐户。

    ![关联的订阅列表](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-hub-storage-account"></a>连接到 Azure Stack 中心存储帐户

你还可以使用存储帐户名称和密钥对连接到 Azure Stack 中心存储帐户。

1. 在存储资源管理器的左窗格中，选择 "管理帐户"。 将显示登录的所有 Microsoft 帐户。

    ![添加帐户-存储资源管理器](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. 若要连接到 Azure Stack 中心订阅，请选择 "**添加帐户**"。

    ![添加帐户-连接到 Azure 存储](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. 在 "连接到 Azure 存储" 对话框中，选择 "**使用存储帐户名称和密钥**"。

4. 在**帐户名称**中输入帐户名称，并将帐户密钥粘贴到 "**帐户密钥**" 文本框中。 然后，在 "**存储终结点域**" 中选择 "**其他" （在下面输入）** 并输入 Azure Stack 中心终结点。

    Azure Stack 中心终结点包括两个部分：区域名称和 Azure Stack 中心域。 在 Azure Stack 开发工具包中，默认终结点是**test-azurestack**。 如果你不确定终结点，请与云管理员联系。

    ![附加名称和密钥](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. 选择“连接”。
6. 成功附加存储帐户后，将显示存储帐户，并在其名称后面附加 "（**外部，其他**）"。

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>后续步骤

* [存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack 中心存储：差异和注意事项](azure-stack-acs-differences.md)
* 若要了解有关 Azure 存储的详细信息，请参阅[Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
