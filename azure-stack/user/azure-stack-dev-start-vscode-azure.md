---
title: 连接到 Azure Stack 在 Visual Studio Code 中使用 Azure 帐户扩展 |Microsoft Docs
description: 作为开发人员，连接到 Azure Stack 在 Visual Studio Code 中使用 Azure 帐户扩展
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: Howto
ms.date: 05/31/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 8e64a570ab45e57e3cf58639bc2ec23d9b9bd81b
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453490"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>连接到 Azure Stack 在 Visual Studio Code 中使用 Azure 帐户扩展

在本文中，我们将引导你完成连接到 Azure Stack 使用 Azure 帐户扩展。 你将需要更新您的 Visual Studio Code (VS Code) 设置。

VS Code 是构建和调试 web 和云应用程序的轻型编辑器。 它使用 ASP.NET Core、 Python、 NodeJS、 Go 和其他开发人员。 使用 Azure 帐户扩展，可以使用单个 Azure 订阅的其他 Azure 扩展筛选使用登录。 扩展，可以在 Azure Cloud Shell 在 VS Code 集成终端中可用。 使用扩展，您可以连接到 Azure Stack 订阅使用这两个 Azure AD (Azure AD) 和 Active Directory 联合身份验证服务 (AD FS) 标识管理器。 这允许您登录到 Azure Stack 中，选择你的订阅，并在 cloud shell 中打开一个新的命令行。 

> [!Note]  
> 您可以为 Active Directory 联合身份验证服务 (AD FS) 环境使用本文中的步骤。 使用你的 AD FS 凭据和终结点。

## <a name="pre-requisites-for-the-azure-account-extension"></a>Azure 帐户扩展的必备组件

1. Azure Stack 环境 1904年内部版本或更高版本
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure 帐户扩展](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack 订阅](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>若要连接到 Azure Stack 的步骤

1. 打开 VS Code。

2. 选择**扩展**左侧角。

3. 在搜索框中输入 `Azure Account`。

4. 选择**Azure 帐户**，然后选择**安装**。

      ![Azure Stack Visual Studio 代码](media/azure-stack-dev-start-vscode-azure/image1.png)

5. 重启 VS Code 以加载该扩展。

6. 检索要连接到 Azure 资源管理器在 Azure Stack 中的元数据。 
    
    Microsoft Azure 资源管理器是一种管理框架，可用于部署、 管理和监视 Azure 资源。
    - 资源管理器 URL 的 Azure Stack 开发工具包 (ASDK) 为： `https://management.local.azurestack.external/` 
    - 对于集成系统资源管理器 URL 为： `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - 将以下文本添加到你的 URL 访问的元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    例如，若要检索你的 Azure 资源管理器终结点的元数据的 URL 可能如下所示： `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    记下返回的 JSON。 你将需要的值`loginEndpoint`和`loginEndgraphEndpointpoint`属性。

7. 按**Ctrl + Shift + P** ，然后选择**首选项：打开用户设置 (JSON)** 。

8. 在代码编辑器中，替换为您的环境中的值更新以下 JSON 代码片段，然后将代码段粘贴到 settings 块。

    - 值：

        | 参数 | 描述 |
        | --- | --- |
        | `tenant-ID` | Azure Stack [租户 ID](../operator/azure-stack-identity-overview.md) 的值。 |
        | `activeDirectoryEndpointUrl` | 这是从 loginEndpoint 属性的 URL。 |
        | `activeDirectoryResourceId` | 这是从 loginEndgraphEndpointpoint 属性的 URL。
        | `resourceManagerEndpointUrl` | 这是根 URL 的 Azure 资源管理器为 Azure Stack。 | 

    - JSON 代码片段：

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "graph audience",
          "resourceManagerEndpointUrl": "Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

8. 保存用户设置，并使用**Ctrl + Shift + P**再一次。 选择**Azure:登录到 Azure 云**。 新的选项， **AzurePPE**，将出现在目标列表。

9. 选择**AzurePPE**。 在浏览器中加载身份验证页面。 登录到你的终结点。

11. 若要测试你已成功登录到 Azure Stack 订阅，请使用**Ctrl + Shift + P** ，然后选择**Azure:选择订阅**和查看是否有你的订阅。

## <a name="commands"></a>命令

| Azure：登录 | 登录到 Azure 订阅 |
| --- | --- |
| Azure：使用设备代码登录 | 登录到 Azure 订阅使用设备代码。 使用此设置在其中登录命令不起作用。 |
| Azure：登录到 Azure 云 | 登录到 Azure 主权云之一中的订阅。 |
| Azure：注销 | 注销你的 Azure 订阅。 |
| Azure：选择订阅 | 选择想要使用的订阅的集。 该扩展仅显示已筛选的订阅中的资源。 |
| Azure：创建帐户 | 如果还没有 Azure 帐户，则可以[注册](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account)一个今天和接收\$200 的免费信用额度。 |
| Azure：打开 Bash in Cloud Shell | 打开新的终端运行 Bash Cloud Shell 中。 |
| Azure：打开 PowerShell in Cloud Shell | 打开新的终端 Cloud Shell 中运行 PowerShell。 |
| Azure：上传到云 Shell | 将文件上传到 Cloud Shell 存储帐户。 |

## <a name="next-steps"></a>后续步骤

[设置 Azure Stack 中的开发环境 ](azure-stack-dev-start.md)