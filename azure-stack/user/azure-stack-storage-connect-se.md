---
title: 连接到 Azure Stack 订阅或存储帐户的存储资源管理器 |Microsoft Docs
description: 了解如何将存储资源管理器连接到 Azure Stack 订阅
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: f7631b049197c0721ffa801c225e1ecdf853524f
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712483"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>将存储资源管理器连接到 Azure Stack 订阅或存储帐户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在本文中，您将了解如何连接到 Azure Stack 订阅和存储帐户使用[Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)。 存储资源管理器是一款独立应用，可用于在 Windows、 macOS 和 Linux 上轻松处理 Azure Stack 存储数据。

> [!NOTE]  
> 有几个工具可用于将数据移进/移出 Azure Stack 存储。 有关详细信息，请参阅[适用于 Azure Stack 存储的数据传输工具](azure-stack-storage-transfer.md)。

如果尚未安装，请[下载存储资源管理器](https://www.storageexplorer.com/)并将其安装。

连接到 Azure Stack 订阅或存储帐户后，可以使用[Azure 存储资源管理器文章](/azure/vs-azure-tools-storage-manage-with-storage-explorer)能够使用 Azure Stack 数据。 

## <a name="prepare-for-connecting-to-azure-stack"></a>为连接到 Azure Stack 做准备

你需要直接访问 Azure Stack 或存储资源管理器的 VPN 连接来访问 Azure Stack 订阅。 若要了解如何设置到 Azure Stack 的 VPN 连接，请参阅[使用 VPN 连接到 Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)。

对于 Azure Stack 开发工具包 (ASDK)，需要导出 Azure Stack 颁发机构根证书。

> [!Note]  
> 对于 ASDK 中，如果您要连接到通过 VPN 将 ASDK，不使用 VPN 安装过程中创建的根证书 (CA.cer)。  这是 DER 编码的证书，它不允许存储资源管理器来检索 Azure Stack 订阅。 请按照以下步骤导出 Base-64 编码证书，以用于存储资源管理器。

### <a name="export-and-then-import-the-azure-stack-certificate"></a>导出然后导入 Azure Stack 证书

导出然后导入 ASDK 的 Azure Stack 证书。 对于集成系统，该证书可公开签名且此步骤不需要。

1. 在 Azure Stack 主机或已与 Azure Stack 建立 VPN 连接的本地计算机上打开 `mmc.exe`。 

2. 在中**文件**，选择**添加/删除管理单元中**。 选择**证书**中可用的管理单元。 

3. 选择“计算机帐户”，然后选择“下一步”。 选择**本地计算机**，然后选择**完成**。

4.  在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 下查找 **AzureStackSelfSignedRootCert**。

    ![通过 mmc.exe 加载 Azure Stack 根证书](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. 右键单击该证书，选择“所有任务” > “导出”，并按说明导出 **Base-64 编码 X.509 (.CER)** 证书。

    导出的证书会在下一步使用。

6. 启动存储资源管理器。 如果您看到**连接到 Azure 存储**对话框框中，将其取消。

7. 上**编辑**菜单中，选择**SSL 证书**，然后选择**导入证书**。 通过文件选取器对话框找到并打开在上一步导出的证书。

    导入证书后，系统会提示重新启动存储资源管理器。

    ![将证书导入到存储资源管理器](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. 存储资源管理器重新启动后，选择**编辑**菜单，然后检查是否**目标 Azure Stack Api**处于选中状态。 如果不可用，则选择**目标 Azure Stack**，然后重启存储资源管理器的更改才会生效。 此配置是必需的，否则无法与 Azure Stack 环境兼容。

    ![确保选中“目标 Azure Stack”](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>使用 Azure AD 连接到 Azure Stack 订阅

使用以下步骤连接到 Azure Stack 订阅，属于 Azure Active Directory (Azure AD) 帐户的存储资源管理器。

1. 在存储资源管理器的左窗格中，选择“管理帐户”。  
    此时会显示你登录的所有 Microsoft 订阅。

2. 若要连接到 Azure Stack 订阅，请选择“添加帐户”。

    ![添加 Azure Stack 帐户](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. 在连接到 Azure 存储对话框下**Azure 环境**，选择**Azure**， **Azure 中国 21Vianet**， **Azure Germany**，**Azure 美国政府版**，或**添加新的环境**。 这取决于所使用的 Azure Stack 帐户。 选择**登录**使用与至少一个活动 Azure Stack 订阅关联的 Azure Stack 帐户登录。

    ![连接到 Azure 存储](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. 使用 Azure Stack 帐户成功登录后，左窗格将填充与该帐户关联的 Azure Stack 订阅。 选择要使用的 Azure Stack 订阅，并选择“应用”。 （选择或清除“所有订阅”复选框会选择所有列出的 Azure Stack 订阅，或者一个都不选。）

    ![填充“自定义云环境”对话框后，选择 Azure Stack 订阅](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

    ![存储帐户列表，其中包括 Azure Stack 订阅帐户](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>使用 AD FS 帐户连接到 Azure Stack 订阅

> [!Note]  
> Azure 联合身份验证服务 (AD FS) 登录体验支持存储资源管理器 1.2.0，或装有 Azure Stack 1804 或更高更新版的更高存储资源管理器版本。
使用以下步骤连接到 Azure Stack 订阅所属的 AD FS 帐户的存储资源管理器。

1. 选择“管理帐户”。 资源管理器会列出你登录到的 Microsoft 订阅。
2. 选择“添加帐户”连接到 Azure Stack 订阅。

    ![添加帐户的存储资源管理器](media/azure-stack-storage-connect-se/add-an-account.png)

3. 选择“**下一步**”。 在“连接到 Azure 存储”对话框中的“Azure 环境”下，选择“使用自定义环境”，然后单击“下一步”。

    ![连接到 Azure 存储](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. 输入 Azure Stack 自定义环境的所需信息。 

    | 字段 | 说明 |
    | ---   | ---   |
    | 环境名称 | 用户可以自定义此字段。 |
    | Azure 资源管理器终结点 | Azure Stack 开发工具包的 Azure 资源管理器资源终结点示例。<br>对于运营商： https://adminmanagement.local.azurestack.external <br> 对于用户： https://management.local.azurestack.external |

    如果您使用的在 Azure Stack 集成系统并不知道管理终结点，请联系您的运营商。

    ![添加帐户的自定义环境](./media/azure-stack-storage-connect-se/custom-environments.png)

5. 选择**登录**以连接到 Azure Stack 帐户与至少一个活动 Azure Stack 订阅相关联。



6. 选择你想要使用，然后选择的 Azure Stack 订阅**应用**。

    ![帐户管理](./media/azure-stack-storage-connect-se/account-management.png)

    左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

    ![关联的订阅列表](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>连接到 Azure Stack 存储帐户

还可以使用存储帐户名称和密钥对连接到 Azure Stack 存储帐户。

1. 在存储资源管理器的左窗格中，选择“管理帐户”。 将显示你登录到的所有 Microsoft 帐户。

    ![添加帐户的存储资源管理器](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. 若要连接到 Azure Stack 订阅，请选择“添加帐户”。

    ![添加帐户-连接到 Azure 存储](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. 在“连接到 Azure 存储”对话框中，选择“使用存储帐户名称和密钥”。

4. 输入帐户名称**帐户名**并粘贴到的帐户密钥**帐户密钥**文本框。 然后，选择**其他 （在下面输入）** 中**存储终结点域**并输入 Azure Stack 终结点。

    Azure Stack 终结点包含两个部分：区域的名称和 Azure Stack 域。 在 Azure Stack 开发工具包中，默认终结点是 **local.azurestack.external**。 如果你不确定你的终结点，请与你的云管理员联系。

    ![附加名称和密钥](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. 选择“连接”。
6. 成功附加存储帐户后，会显示该存储帐户，其名称后面追加了“(外部、其他)”字样。

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>后续步骤

* [存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack 存储：差异和注意事项](azure-stack-acs-differences.md)
* 若要了解有关 Azure 存储的详细信息，请参阅[Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
