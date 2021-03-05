---
title: 在 Azure Stack Hub 上将 Azure 登录操作与 Azure CLI 和 PowerShell 配合使用
description: 在 Azure Stack Hub 上将 Azure 登录操作与 Azure CLI 和 PowerShell 配合使用，以创建持续集成、持续部署 (CI/CD) 工作流
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 1421917f870d09d61f665a2cee6eb9b617ae75f3
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187344"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>在 Azure Stack Hub 上将 Azure 登录操作与 Azure CLI 和 PowerShell 配合使用

你可以设置 GitHub Actions 以登录到你的 Azure Stack Hub 实例，运行 PowerShell，然后运行 Azure CLI 脚本。 你可以在此基础上通过 Azure Stack Hub 为你的解决方案实现持续集成、持续部署 (CI/CD) 工作流。 利用此工作流，你可以自动构建、测试和部署你的解决方案，以便专注于代码编写。 例如，通过添加一些其他操作，你可以使用此工作流和 Azure 资源管理器模板来预配 VM，验证应用程序存储库，然后在每次合并到 GitHub 中的特定分支时将应用程序部署到该 VM。 现在，本文将帮助你了解 GitHub Actions 和 Azure Stack Hub。

GitHub Actions 是由代码存储库内用于实现自动化的操作组成的工作流。 在 GitHub 开发过程中，你可以使用事件来触发工作流。 你可以执行常见的 DevOps 自动化任务，例如测试、部署和持续集成。

若要将 GitHub Actions 与 Azure Stack Hub 配合使用，必须使用具有特定要求的服务主体 (SPN)。 在本文中，你将创建一个自承载运行器。 GitHub 允许在 GitHub Actions 中使用可供 GitHub 访问的任何计算机。 你可以在 Azure、Azure Stack Hub 或其他地方创建虚拟机 (VM) 作为你的运行器。

此示例工作流包括：
- 有关创建和验证 SPN 的说明。
- 将 Windows 2016 Server Core 计算机配置为 GitHub Actions 自承载运行器，使之与 Azure Stack Hub 一起工作。
- 一个工作流，它使用：
    - Azure 登录操作
    - PowerShell 脚本操作

### <a name="azure-stack-hub-github-actions"></a>Azure Stack Hub GitHub Actions

下图显示了不同的环境及其关系。

使用自承载运行器的 ![Azure Stack Hub Github 操作](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg)部分：

- GitHub 上承载的 GitHub Actions
- Azure 上承载的自承载运行器
- Azure Stack Hub

将 GitHub Actions 与 Azure Stack Hub 配合使用的限制是，该过程需要使用连接到 Web 的 Azure Stack Hub。 工作流在 GitHub 存储库中触发。 你可以使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识提供者。

你的自承载运行器也可以使用虚拟专用网络连接到防火墙后面的 Azure Stack Hub，但这超出了本文的范围。

## <a name="get-service-principal"></a>获取服务主体

SPN 提供基于角色的凭据，以便 Azure 外部的进程可以连接到资源并与之交互。 你将需要一个具有参与者访问权限的 SPN，并需要这些说明中指定的与 GitHub Actions 一起使用的属性。

作为 Azure Stack Hub 用户，你无权创建 SPN。 你需要从云操作员处请求此主体。 此处提供了相关说明，因此，如果你是云操作员，则可以创建 SPN；如果你是开发人员并在工作流中使用云操作员提供的 SPN，则可以验证 SPN。

云操作员需要使用 Azure CLI 创建 SPN。

下面的代码片段是面向将 PowerShell 提示符与 Azure CLI 配合使用的 Windows 计算机编写的。 如果你在 Linux 计算机和 bash 上使用 CLI，请删除行扩展或将其替换为 `\`。

1. 准备用于创建 SPN 的以下参数的值：

    | 参数 | 示例 | 说明 |
    | --- | --- | --- |
    endpoint-resource-manager | "https://management.orlando.azurestack.corp.microsoft.com"  | 资源管理终结点。 |
    suffix-storage-endpoint | "orlando.azurestack.corp.microsoft.com"  | 存储帐户的终结点后缀。 |
    suffix-keyvault-dns | ".vault.orlando.azurestack.corp.microsoft.com"  | Key Vault 服务 DNS 后缀。 |
    endpoint-active-directory-graph-resource-id | "https://graph.windows.net/"  | Active Directory 资源 ID。 |
    endpoint-sql-management | https://notsupported  | SQL Server 管理终结点。 将其设置为 `https://notsupported` |
    个人资料 | 2019-03-01-hybrid | 要用于此云的配置文件。 |

2. 打开你的命令行工具（例如 Windows PowerShell 或 Bash）并登录。 使用以下命令：

    ```azurecli  
    az login
    ```

3. 对于新环境，请使用 `register` 命令；对于现有环境，请使用 `update` 命令。 使用以下命令。

    ```azurecli  
    az cloud register `
        -n "AzureStackUser" `
        --endpoint-resource-manager "https://management.<local>.<FQDN>" `
        --suffix-storage-endpoint ".<local>.<FQDN>" `
        --suffix-keyvault-dns ".vault.<local>.<FQDN>" `
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" `
        --endpoint-sql-management https://notsupported  `
        --profile 2019-03-01-hybrid
    ```

4. 获取要用于 SPN 的订阅 ID 和资源组。

5. 通过以下命令使用订阅 ID 和资源组创建 SPN：

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

6. 检查生成的 JSON 对象。 你将使用此 JSON 对象在包含你的操作的 GitHub 存储库中创建机密。 JSON 对象应具有以下属性：

    ```json
    {
      "clientId": <Application ID for the SPN>,
      "clientSecret": <Client secret for the SPN>,
      "subscriptionId": <Subscription ID for the SPN>,
      "tenantId": <Tenant ID for the SPN>,
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com/",
      "resourceManagerEndpointUrl": "https://management.<FQDN>",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://notsupported",
      "galleryEndpointUrl": "https://providers.<FQDN>:30016/",
      "managementEndpointUrl": "https://management.<FQDN>"
    }
    ```

## <a name="add-service-principal-to-repository"></a>将服务主体添加到存储库

你可以使用 GitHub 机密对要在操作中使用的敏感信息进行加密。 你将创建一个机密来包含你的 SPN，以便该操作可以登录到 Azure Stack Hub 实例。

> [!WARNING]  
> GitHub 建议你不要将自承载运行器与公共存储库一起使用。公共存储库的分支可能会通过创建一个在工作流中执行代码的拉取请求，在自承载运行器计算机上运行危险的代码。 有关详细信息，请参阅[关于自承载运行器](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories)。

1. 打开或创建 GitHub 存储库。 如果你需要有关在 GitHub 中创建存储库的指南，可以查找 [GitHub 文档中的说明](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo)。
1. 将你的存储库设置为专用的。
    1. 选择“设置” > “更改存储库可见性”。 
    1. 选择“设为专用”。
    1. 键入你的存储库的名称。
    1. 选择“我了解，更改存储库可见性”。
1. 选择“设置”。
1. 选择“机密”。
1. 选择“新建存储库机密”。
    ![添加你的 GitHub Actions 机密](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. 将你的机密命名为 `AZURE_CREDENTIALS`。
1. 粘贴表示你的 SPN 的 JSON 对象。
1. 选择“添加机密”。

## <a name="create-your-vm-and-install-prerequisites"></a>创建你的 VM 并安装必备组件

1. 创建你的自承载运行器。 

    这些说明将运行器创建为 Azure 中的 Windows VM。 如果你需要连接到数据中心内承载的 Azure Stack Hub，则你可能需要一个 VPN 连接。 你可以从[在自承载运行器上安装 Azure Stack Hub 工具](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner)部分中找到有关如何启用连接的说明，该运行器可能需要 VPN 连接。
    - 有关在 Azure 中创建 Windows VM 的指南，请参阅[快速入门：在 Azure 门户中创建 Windows 虚拟机](/azure/virtual-machines/windows/quick-create-portal)。 按照这些说明操作时，请安装 Windows Server 2016 Core。
    - 有关在 Azure Stack Hub 中创建 Windows VM 的指南，请参阅[快速入门：使用 Azure Stack Hub 门户创建 Windows 服务器 VM](./azure-stack-quick-windows-portal.md)。 按照这些说明操作时，请安装 Windows Server 2016 Core。
1. 借助远程连接，使用创建计算机时定义的服务器 IP 地址、用户名和密码连接到你的 Windows 2016 服务器。
1. 安装 Chocolatey。 Chocolatey 是适用于 Windows 的包管理器，可用于从命令行安装和管理依赖项。 从已提升权限的 PowerShell 提示符下，键入：
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. 安装 PowerShell Core。 从已提升权限的 PowerShell 提示符下，键入：
    ```powershell  
    choco install powershell-core
    ```
1. 安装 Azure CLI。 从已提升权限的 PowerShell 提示符下，键入：
    ```powershell  
    choco install azure-cli
    ```
1. 安装 Azure Stack Hub PowerShell。 从已提升权限的 PowerShell 提示符下，键入：
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    若要详细了解如何使用 Azure Stack Hub Az 模块，请参阅[安装适用于 Azure Stack Hub 的 PowerShell Az 模块](../operator/powershell-install-az-module.md)。
7. 重启计算机。 从已提升权限的 PowerShell 提示符下，键入：
    ```powershell  
    shutdown /r
    ```
8. 将该计算机作为自承载运行器添加到你的 GitHub 存储库。 你可以在 GitHub 文档中找到有关如何添加自承载运行器的说明。有关详细信息，请参阅[添加自承载运行器](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners)。

    ![运行器正在侦听](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. 完成后，验证该服务是否正在运行并且正在侦听你的服务。 通过从运行器的目录运行 `/run.cmd` 进行复查。

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>可选：在自承载运行器上安装 Azure Stack Hub 工具

本文中的说明不需要访问 [Azure Stack Hub 工具](../operator/azure-stack-powershell-download.md?tabs=az)，但当你开发自己的工作流时，可能需要使用这些工具。 以下说明可帮助你在 Windows 自承载运行器上安装这些工具。 有关 Azure Stack Hub 工具的详细信息，请参阅[从 GitHub 下载 Azure Stack Hub 工具](../operator/azure-stack-powershell-download.md?tabs=az)。 这些说明假定你已安装了包管理器 Chocolatey。

1. 安装 Git。
    ```powershell  
    choco install git
    ```

2. 从已提升权限的 PowerShell 提示符下，键入：
    ```powershell
    # Change directory to the root directory.
    cd \
    
    # Download the tools archive.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
    invoke-webrequest `
      https://github.com/Azure/AzureStack-Tools/archive/az.zip `
      -OutFile az.zip
    
    # Expand the downloaded files.
    expand-archive az.zip `
      -DestinationPath . `
      -Force
    
    # Change to the tools directory.
    cd AzureStack-Tools-az
    ```

3. 如果你需要让运行器连接到 Azure Stack Hub 实例，则可以使用 PowerShell。 可以在[使用 PowerShell 连接到 Azure Stack Hub](../operator/azure-stack-powershell-configure-admin.md?tabs=az1%2Caz2%2Caz3) 一文中找到这些说明。

## <a name="create-a-self-hosted-runner"></a>创建自承载运行器

可以在 GitHub 文档中设置自承载运行器。自承载运行器可以在可连接到 GitHub 的任何计算机上运行。 如果工作流中的自动化任务需要广泛的依赖项、特定的许可要求（例如软件许可证的 USB 硬件保护装置）或其他特定于计算机或软件的需求，则可以选择使用自承载运行器。 你的计算机可以是物理计算机、VM 或容器。 可以将运行器置于你的数据中心或云中。

在本文中，你将使用 Azure 中承载的 Windows VM，该 VM 将配置为具有 Azure Stack Hub 特定的 PowerShell 要求。

有关设置、配置自承载运行器并将其连接到你的存储库的说明，请参阅 GitHub 文档“[关于自承载运行器](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners)”。

![已连接的自承载运行器](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

记下你的自承载运行器的名称和标记。 本文中的工作流将使用标记 `self-hosted` 来调用它。

## <a name="add-the-workflow-to-your-repository"></a>将工作流添加到存储库

创建新的工作流（使用本部分中的 yaml 来创建工作流）。

1. 打开你的 GitHub 存储库。
2. 选择“操作”。
3. 创建一个新工作流。
    - 如果这是你的第一个工作流，请在“选择工作流模板”下选择“自己设置工作流” 。
    - 如果你已有工作流，请选择“新建工作流” > “自己设置工作流”。 

4. 在路径中，将文件命名为 `workflow.yml`。
5. 复制并粘贴工作流 yml。
    ```yaml  
    on: [push]
    
    env:
      ACTIONS_ALLOW_UNSECURE_COMMANDS: 'true'
     
    jobs: 
      azurestack-test:
        runs-on: self-hosted
        steps:
    
          - name: Login to AzureStack with Az Powershell
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: true
          
          - name: Run Az PowerShell Script Against AzureStack
            uses: azure/powershell@v1
            with:
              azPSVersion: '3.1.0'
              inlineScript: |
                hostname
                Get-AzContext
                Get-AzResourceGroup
          
          - name: Login to AzureStack with CLI
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: false
          
          - name: Run Azure CLI Script Against AzureStack
            run: |
              hostname
              az group list --output table
    ```
6. 选择“开始提交”。
7. 添加提交标题和可选的详细信息，然后选择“提交新文件”。

当操作运行时，验证它是否已成功运行。

1. 打开你的 GitHub 存储库。 可以通过推送到存储库来触发工作流。
1. 选择“操作”。
1. 在“所有工作流”下选择提交的名称。

    ![查看提交摘要](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. 选择作业的名称“azurestack-test”。

    ![查看提交详细信息](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. 展开各个部分以查看你的 PowerShell 和 CLI 命令的返回值。

有关工作流文件和操作的说明：

- 工作流包含名为 `azurestack-test` 的单个作业。
- 推送事件会触发工作流。
- 此操作使用已在存储库中设置的自承载运行器，你可以在工作流中使用以下行通过运行器的标签来调用该运行器：`runs on: self-hosted`。
- 工作流包含三个操作。
- 第一个操作调用 Azure 登录操作，通过 PowerShell 借助适用于 Azure 的 GitHub Actions 进行登录。你可以创建可以在存储库中设置的工作流，以便在 Azure 中进行生成、测试、打包、发布和部署操作。 此操作使用你的 Azure Stack SPN 凭据连接到 Azure Stack Hub 环境并打开与它的会话。 你可以在 GitHub [Azure 登录操作](https://github.com/marketplace/actions/azure-login)中找到有关如何使用此操作的详细信息。
- 第二个操作使用 Azure PowerShell。 此操作使用 Az PowerShell 模块，用于政府云和 Azure Stack Hub 云。 运行此工作流后，请查看作业，以便验证脚本是否已收集你的 Azure Stack Hub 环境中的资源组。 有关详细信息，请参阅 [Azure PowerShell 操作](https://github.com/marketplace/actions/azure-powershell-action)
- 第三个操作使用 Azure CLI 登录并连接到 Azure Stack Hub 来收集资源组。 有关详细信息，请参阅 [Azure CLI 操作](https://github.com/marketplace/actions/azure-cli-action)。
- 若要详细了解如何使用 GitHub Actions 和自承载运行器，请参阅 [GitHub Actions](https://github.com/features/actions) 文档。

## <a name="next-steps"></a>后续步骤

- 在 [GitHub 市场](https://github.com/marketplace)中查找更多操作。
- 了解 [Azure Stack Hub 的常见部署](azure-stack-dev-start-deploy-app.md)  
- 了解[在 Azure Stack Hub 中使用 Azure 资源管理器模板](azure-stack-arm-templates.md)  
- 查看 DevOps 混合云模式：[DevOps 模式](/hybrid/app-solutions/pattern-cicd-pipeline)
