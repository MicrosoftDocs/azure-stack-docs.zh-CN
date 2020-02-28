---
title: 使用 Azure 帐户扩展连接到 Azure Stack 中心 Visual Studio Code
description: 作为开发人员，请在 Visual Studio Code 中使用 Azure 帐户扩展连接到 Azure Stack 中心
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 934aa01fa02d763ce170df219dfee3770d502d22
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703037"
---
# <a name="connect-to-azure-stack-hub-using-azure-account-extension-in-visual-studio-code"></a>使用 Azure 帐户扩展连接到 Azure Stack 中心 Visual Studio Code

本文将介绍如何使用 Azure 帐户扩展连接到 Azure Stack 中心。 需要更新 Visual Studio Code （VS Code）设置。

VS Code 是用于构建和调试 web 应用程序和云应用程序的轻型编辑器。 ASP.NET Core、Python、NodeJS、中转和其他开发人员使用 VS Code。 使用 Azure 帐户扩展，可以使用单个 Azure 登录，其中包含订阅筛选功能，以用于其他 Azure 扩展。 扩展使 Azure Cloud Shell 在 VS Code 集成终端中可用。 使用该扩展，可以使用 Azure AD （Azure AD）和 Active Directory 联合服务（AD FS）为标识管理器连接到 Azure Stack 中心订阅。 你可以登录到 Azure Stack 中心，选择你的订阅，然后在 cloud shell 中打开新的命令行。 

> [!Note]  
> 你可以使用本文中的步骤来 Active Directory 联合服务（AD FS）环境。 使用 AD FS 凭据和终结点。

## <a name="pre-requisites-for-the-azure-account-extension"></a>Azure 帐户扩展的先决条件

1. Azure Stack 集线器环境1904版本或更高版本
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure 帐户扩展](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack 中心订阅](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器的步骤

1. 从 GitHub 中 Azure Stack 集线器工具运行**标识**脚本。

    - 在运行该脚本之前，你将需要为你的环境安装并配置 PowerShell。 有关说明，请参阅为[Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)。

    - 有关**标识**脚本说明和脚本，请参阅[test-azurestack-工具/标识](https://aka.ms/aa6z611)。

    - 在同一会话中，运行：

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. 打开 VS Code。

3. 选择左侧角的 "**扩展**"。

4. 在搜索框中输入 `Azure Account`。

5. 选择 " **Azure 帐户**" 并选择 "**安装**"。

      ![Azure Stack 中心 Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

6. 重新启动 VS Code 以加载扩展。

7. 检索元数据以连接到 Azure Stack 中心的 Azure 资源管理器。 
    
    Microsoft Azure 资源管理器是一种管理框架，可用于部署、管理和监视 Azure 资源。
    - Azure Stack 开发工具包的资源管理器 URL （ASDK）为： `https://management.local.azurestack.external/` 
    - 集成系统的资源管理器 URL 是： `https://management.region.<fqdn>/`，其中 `<fqdn>` 是完全限定的域名。
    - 将以下文本添加到 URL 以访问元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    例如，检索 Azure 资源管理器终结点的元数据的 URL 可能类似如下所示： `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    记下返回的 JSON。 需要 `loginEndpoint` 和 `audiences` 属性的值。

8. 按**Ctrl + Shift + P，** 然后选择 "**首选项：打开用户设置（JSON）** "。

9. 在代码编辑器中，将以下 JSON 代码片段更新为环境的值，然后将代码片段粘贴到 settings 块。

    - 值：

        | 参数 | 说明 |
        | --- | --- |
        | `tenant-ID` | Azure Stack 中心[租户 ID](../operator/azure-stack-identity-overview.md)的值。 |
        | `activeDirectoryEndpointUrl` | 这是来自 loginEndpoint 属性的 URL。 |
        | `activeDirectoryResourceId` | 这是来自访问群体属性的 URL。
        | `resourceManagerEndpointUrl` | 这是适用于 Azure Stack 中心的 Azure 资源管理器的根 URL。 | 

    - JSON 代码片段：

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

10. 保存用户设置，并再次使用**Ctrl + Shift + P** 。 选择 **"azure：登录到 Azure 云"** 。 新选项**AzurePPE**将出现在目标列表中。

11. 选择**AzurePPE**。 身份验证页在浏览器中加载。 登录到你的终结点。

12. 若要测试是否已成功登录到 Azure Stack 中心订阅，请按**Ctrl + Shift + P** ，然后选择 " **Azure：选择订阅**"，并查看是否有可用的订阅。

## <a name="commands"></a>命令

| Azure：登录 | 登录到 Azure 订阅 |
| --- | --- |
| Azure：通过设备代码登录 | 使用设备代码登录到你的 Azure 订阅。 在 "登录" 命令不起作用的设置中使用设备代码。 |
| Azure：登录到 Azure 云 | 在其中一个主权云登录到你的 Azure 订阅。 |
| Azure：注销 | 注销 Azure 订阅。 |
| Azure：选择订阅 | 选择要使用的订阅集。 该扩展只显示已筛选订阅中的资源。 |
| Azure：创建帐户 | 如果你没有 Azure 帐户，可以立即[注册](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account)一位，并获得免费的 \$200。 |
| Azure：在 Cloud Shell 中打开 Bash | 在 Cloud Shell 中打开运行 Bash 的新终端。 |
| Azure：在 Cloud Shell 中打开 PowerShell | 在 Cloud Shell 中打开运行 PowerShell 的新终端。 |
| Azure：上传到 Cloud Shell | 将文件上传到 Cloud Shell 的存储帐户。 |

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 中心设置开发环境](azure-stack-dev-start.md)
