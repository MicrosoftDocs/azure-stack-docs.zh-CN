---
title: 在 Azure Stack 中心将 Azure 登录操作与 Azure CLI 和 PowerShell 配合使用
description: 将 Azure 登录操作与 Azure CLI 和 PowerShell 一起使用，以在 Azure Stack 中心 (CI/CD) 工作流创建持续集成和持续部署
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 4413070dc3d55a7a879b5c4589d9f453a617e0e0
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224606"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>在 Azure Stack 中心将 Azure 登录操作与 Azure CLI 和 PowerShell 配合使用

可以设置 GitHub 操作以登录到 Azure Stack 中心实例，运行 PowerShell，然后运行 Azure CLI 脚本。 你可以将此作为持续集成、持续部署 (CI/CD) 工作流的基础，Azure Stack 中心为你的解决方案。 利用此工作流，你可以自动生成、测试和部署你的解决方案，以便你可以专注于编写代码。 例如，通过添加一些其他操作，你可能会使用此工作流以及 Azure 资源管理器模板来预配 VM，验证应用程序存储库，然后在每次合并到 GitHub 中的特定分支时，将应用程序部署到该 VM。 现在，本文将帮助你了解 GitHub 操作和 Azure Stack 中心。

GitHub 操作是由在代码存储库内启用自动执行的操作组成的工作流。 你可以在 GitHub 开发过程中用事件来触发工作流。 可以执行常见的 DevOps 自动化任务，如测试、部署和持续集成。

若要将 GitHub 操作与 Azure Stack 中心一起使用，必须将服务主体 (SPN) 与特定要求一起使用。 本文介绍如何创建 *自承载的运行* 程序。 GitHub 允许在 GitHub 操作中使用 GitHub 可以访问的任何计算机。 可以在 Azure 中的运行程序) 、Azure Stack 中心或其他地方创建虚拟机 (VM。

此示例工作流包括：
- 有关创建和验证 SPN 的说明。
- 将 Windows 2016 Server core 计算机配置为 GitHub 操作自承载运行程序，以便与 Azure Stack 中心一起工作。
- 使用的工作流：
    - Azure 登录操作
    - PowerShell 脚本操作

### <a name="azure-stack-hub-github-actions"></a>Azure Stack 中心 GitHub 操作

下图显示了不同的环境和它们之间的关系。

![](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg)使用自承载运行程序的 Azure Stack 集线器 Github 操作部分：

- GitHub 上托管的 GitHub 操作
- 托管在 Azure 上的自承载运行程序
- Azure Stack Hub

Azure Stack 中心使用 GitHub 操作的限制是，该过程需要使用连接到 web 的 Azure Stack 中心。 在 GitHub 存储库中触发工作流。 你可以使用 Azure Active Directory (Azure AD) 或 Active Directory 联合服务 (AD FS) 作为标识提供者。

虽然这超出了本文的范围，但你的自承载运行程序还可以使用虚拟专用网络连接到防火墙后面的 Azure Stack 集线器。

## <a name="get-service-principal"></a>获取服务主体

SPN 提供基于角色的凭据，以便 Azure 外部的进程可以连接到资源并与之进行交互。 你将需要一个具有参与者访问权限的 SPN，并需要在这些说明中指定的属性与 GitHub 操作一起使用。

作为 Azure Stack 中心的用户，你没有创建 SPN 的权限。 需要从云操作员请求此原则。 此处提供了说明，以便你可以在你是云操作员的情况下创建 SPN，或者，如果你是开发人员在云操作员提供的工作流中使用 SPN，则可以验证 SPN。

Cloud 操作员需要使用 Azure CLI 创建 SPN。

以下代码段是使用带有 Azure CLI 的 PowerShell 提示符为 Windows 计算机编写的。 如果在 Linux 计算机和 bash 上使用 CLI，请删除行扩展，或将其替换为 `\` 。

1. 准备用于创建 SPN 的以下参数的值：

    | 参数 | 示例 | 描述 |
    | --- | --- | --- |
    终结点-资源管理器 | "https://management.orlando.azurestack.corp.microsoft.com"  | 资源管理终结点。 |
    后缀-存储终结点 | "orlando.azurestack.corp.microsoft.com"  | 存储帐户的终结点后缀。 |
    keyvault-dns | ". vault.orlando.azurestack.corp.microsoft.com"  | "Key Vault 服务" dns 后缀。 |
    终结点-active directory-资源 id | "https://graph.windows.net/"  | Active Directory 资源 ID。 |
    终结点-sql-管理 | https://notsupported  | Sql server 管理终结点。 将其设置为 `https://notsupported` |
    个人资料 | 2019-03-01-hybrid | 要用于此云的配置文件。 |

2. 打开命令行工具，例如 Windows PowerShell 或 Bash 并登录。 使用以下命令：

    ```azurecli  
    az login
    ```

3. `register`如果使用现有的环境，请将命令用于新环境或 `update` 命令。 使用以下命令。

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

5. 在以下命令中创建 SPN，其中包含订阅 ID 和资源组：

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

6. 检查生成的 JSON 对象。 你将使用 JSON 对象在包含你的操作的 GitHub 存储库中创建机密。 JSON 对象应具有以下属性：

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

您可以使用 GitHub 机密对要在操作中使用的敏感信息进行加密。 你将创建一个包含 SPN 的机密，以便该操作可以登录到 Azure Stack 中心实例。

> [!WARNING]  
> GitHub 建议你不要使用具有公共存储库分支的自承载的流，公共存储库分支可以通过创建在工作流中执行代码的拉取请求，在自承载运行程序计算机上运行危险代码。 有关详细信息，请参阅 "[关于自承载的流](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories)"。

1. 打开或创建 GitHub 存储库。 如果需要有关在 GitHub 中创建存储库的指南，可以 [在 github 文档中找到相关说明](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo)。
1. 将存储库设置为 "专用"。
    1. 选择 "**设置**" "  >  **更改存储库可见性**"。
    1. 选择 " **设为专用**"。
    1. 键入存储库的名称。
    1. 选择 " **我了解"，更改存储库可见性**。
1. 选择“设置”。
1. 选择 " **机密**"。
1. 选择 " **新建存储库机密**"。
    ![添加 GitHub 操作机密](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. 命名你的机密 `AZURE_CREDENTIALS` 。
1. 粘贴代表 SPN 的 JSON 对象。
1. 选择“添加机密”。

## <a name="create-your-vm-and-install-prerequisites"></a>创建 VM 并安装必备组件

1. 创建自承载的运行程序。 

    这些说明将运行程序创建为 Azure 中的 Windows VM。 如果需要连接到数据中心内托管的 Azure Stack 中心，可能需要 VPN 连接。 可以在可能需要 VPN 连接的 [自承载运行程序上的安装 Azure Stack 集线器工具](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner) 部分中找到有关启用连接的说明。
    - 有关在 Azure 中创建 Windows VM 的指南，请参阅 [快速入门：在 Azure 门户中创建 windows 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)。 按照这些说明操作时，请安装 Windows Server 2016 Core。
    - 有关在 Azure Stack Hub 中创建 Windows VM 的指南，请参阅 [快速入门：使用 Azure Stack 中心门户创建 windows SERVER vm](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)。 按照这些说明操作时，请安装 Windows Server 2016 Core。
1. 使用远程连接连接到使用在创建计算机时定义的服务器 IP 地址、用户名和密码的 Windows 2016 服务器。
1. 安装 Chocolatey。 Chocolatey 是适用于 Windows 的程序包管理器，可用于从命令行安装和管理依赖项。 在提升的 PowerShell 提示符下，键入：
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. 安装 PowerShell Core。 在提升的 PowerShell 提示符下，键入：
    ```powershell  
    choco install powershell-core
    ```
1. 安装 Azure CLI。 在提升的 PowerShell 提示符下，键入：
    ```powershell  
    choco install azure-cli
    ```
1. 安装 Azure Stack Hub PowerShell。 在提升的 PowerShell 提示符下，键入：
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    有关使用 Azure Stack 集线器 Az 模块的详细信息，请参阅 [安装适用于 Azure Stack 中心的 PowerShell Az 模块](https://docs.microsoft.com/azure-stack/operator/powershell-install-az-module)。
7. 重启计算机。 在提升的 PowerShell 提示符下，键入：
    ```powershell  
    shutdown /r
    ```
8. 将计算机作为自承载运行程序添加到 GitHub 存储库。 你可以在 GitHub 文档中找到有关添加自承载运行程序的说明。有关详细信息，请参阅 [添加自承载的流](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners)。

    ![运行程序正在侦听](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. 完成后，请验证该服务是否正在运行，并且正在侦听你的服务。 通过从运行程序的目录运行来进行双重检查 `/run.cmd` 。

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>可选：在自承载运行程序上安装 Azure Stack 集线器工具

本文中的说明不需要访问 [Azure Stack 中心工具](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?&tabs=az)，但当你开发自己的工作流时，可能需要使用这些工具。 以下说明可帮助你在 Windows 自承载运行程序上安装工具。 有关 Azure Stack 中心工具的详细信息，请参阅 [从 GitHub 下载 Azure Stack 中心工具](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?&tabs=az)。 这些说明假定已安装包管理器 Chocolatey。

1. 安装 Git。
    ```powershell  
    choco install git
    ```

2. 在提升的 PowerShell 提示符下，键入：
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

3. 如果需要运行程序连接到 Azure Stack 中心实例，则可以使用 PowerShell。 可以在 [通过 PowerShell 连接到 Azure Stack 集线器](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-configure-admin?&tabs=az1%2Caz2%2Caz3)一文中找到相关说明。

## <a name="create-a-self-hosted-runner"></a>创建自承载运行程序

可以在 GitHub 文档中设置自承载的运行程序。自承载的运行程序可以在可连接到 GitHub 的任何计算机上运行。 如果工作流中的自动化任务需要广泛的依赖项、特定的许可要求（如软件许可证的 USB 转换器）或其他计算机或软件特定的需求，则可以选择使用自承载的运行程序。 计算机可以是物理计算机、VM 或容器。 可以将运行程序放入数据中心或云中。

在本文中，你将使用 Azure 中托管的 Windows VM，该 VM 将配置为具有 Azure Stack 集线器特定的 PowerShell 要求。

有关设置、配置您的自承载运行程序并将其连接到您的存储库的说明，请参阅 GitHub 文档 "[关于自承载的](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners)资源库"。

![已连接自承载运行程序](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

记下自承载运行程序的名称和标记。 本文中的工作流将使用标记来调用它 `self-hosted` 。

## <a name="add-the-workflow-to-your-repository"></a>将工作流添加到存储库

使用此部分中的 yaml 创建一个新的工作流，以创建工作流。

1. 打开 GitHub 存储库。
2. 选择 **操作**。
3. 创建新工作流。
    - 如果这是你的第一个工作流，请在 "**选择工作流模板**" 下选择 "**设置工作流**"。
    - 如果现有工作流，请选择 "**新建工作** 流" "  >  **自行设置工作流**"。

4. 在路径中，为文件命名 `workflow.yml` 。
5. 复制并粘贴工作流 docker-compose.override.yml。
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
7. 添加提交标题和可选详细信息，然后选择 " **提交新文件**"。

当操作运行时，验证它是否已成功运行。

1. 打开 GitHub 存储库。 可以通过推送到存储库来触发工作流。
1. 选择 **操作**。
1. 在 " **所有工作流**" 下选择提交的名称。

    ![查看提交摘要](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. 选择作业的名称， **test-azurestack**。

    ![查看提交详细信息](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. 展开部分以查看 PowerShell 和 CLI 命令的返回值。

工作流文件和操作的说明：

- 该工作流包含一个名为的作业 `azurestack-test` 。
- 推送事件会触发工作流。
- 操作使用已在存储库中设置的自承载运行程序，并通过工作流中的运行程序的标签（行：）在中调用 `runs on: self-hosted` 。
- 该工作流包含三个操作。
- 第一项操作调用 Azure 登录操作来通过具有适用于 Azure 的 GitHub 操作的 PowerShell 进行登录，可以创建可在存储库中设置的工作流，以生成、测试、打包、发布和部署到 Azure。 此操作使用 Azure Stack SPN 凭据连接到 Azure Stack 中心环境，并打开会话。 有关使用此操作的详细信息，请查看 GitHub [Azure 登录操作](https://github.com/marketplace/actions/azure-login)。
- 第二个操作使用 Azure PowerShell。 该操作使用 Az PowerShell 模块，并与政府和 Azure Stack 中心云一起工作。 运行此工作流后，请查看作业，验证脚本是否已在 Azure Stack 中心环境中收集了资源组。 有关详细信息，请参阅 [Azure PowerShell 操作](https://github.com/marketplace/actions/azure-powershell-action)
- 第三个操作使用 Azure CLI 登录并连接到 Azure Stack 中心，以收集资源组。 有关详细信息，请参阅 [Azure CLI 操作](https://github.com/marketplace/actions/azure-cli-action)。
- 有关使用 GitHub 操作和自承载运行程序的详细信息，请参阅 [Github 操作](https://github.com/features/actions) 文档。

## <a name="next-steps"></a>后续步骤

- 在 [GitHub Marketplace](https://github.com/marketplace)中查找更多操作。
- 了解 [Azure Stack 中心的常见部署](azure-stack-dev-start-deploy-app.md)  
- 了解如何 [在 Azure Stack 中心使用 Azure 资源管理器模板](azure-stack-arm-templates.md)  
- 查看 DevOps 混合云模式 [DevOps 模式](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline)
