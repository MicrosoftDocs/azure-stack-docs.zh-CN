---
title: 将 Azure web 应用部署操作与 Azure Stack 中心配合使用
description: 使用 Azure web 应用部署操作创建持续集成、持续部署 (CI/CD) 工作流到 Azure Stack 中心
author: mattbriggs
ms.topic: how-to
ms.date: 2/16/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 2/16/2021
ms.openlocfilehash: fec9acb7a3e156a8646aef88c53e681eed9e53da
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655249"
---
# <a name="use-the-azure-web-app-deploy-action-with-azure-stack-hub"></a>将 Azure web 应用部署操作与 Azure Stack 中心配合使用

可以设置 GitHub 操作，将 web 应用部署到 Azure Stack 中心实例。 这使你可以为应用程序设置持续集成和部署。 本文将帮助你使用 GitHub 操作和 Azure Stack 中心实现自动部署。 你将创建一个 web 应用，并使用发布配置文件来创建用于托管你的应用程序的 web 应用程序。

GitHub 操作是由在代码存储库中启用自动执行的操作组成的工作流。 在 GitHub 开发过程中，你可以使用事件来触发工作流。 可以设置常见的 DevOps 自动化任务，如测试、部署和持续集成。

此示例工作流包括：
- 有关创建和验证服务主体 (SPN) 的说明。
- 有关创建 web 应用发布配置文件的说明
- 添加特定于运行时的工作流
- 使用 web 应用部署添加匹配的工作流
## <a name="get-service-principal"></a>获取服务主体

SPN 提供基于角色的凭据，以便 Azure 外部的进程可以连接到资源并与之交互。 你将需要一个具有参与者访问权限的 SPN，并需要在这些说明中指定的属性与 GitHub 操作一起使用。

作为 Azure Stack 中心的用户，你无权创建 SPN。 需要从云操作员请求此原则。 此处提供了相关说明，因此，如果你是云操作员，可以创建 SPN。 或者，如果你是开发人员，则可以验证云操作员提供的 SPN。

云操作员需要使用 Azure CLI 创建 SPN。

下面的代码片段是面向将 PowerShell 提示符与 Azure CLI 配合使用的 Windows 计算机编写的。 如果在 Linux 计算机和 bash 上使用 CLI，请删除行扩展，或将其替换为 `\` 。

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

3. 如果使用的是现有环境，请将 `register` 命令用于新环境或 `update` 命令。 使用以下命令。

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

    如果没有云操作员权限，还可以使用云操作员提供的 SPN 登录。 你需要 "客户端 ID"、"机密" 和 "租户 ID"。 使用这些值，你可以使用以下 Azure CLI 命令创建可作为机密添加到 GitHub 存储库的 JSON 对象。

    ```azurecli  
    az login --service-principal -u "<client-id>" -p "<secret>" --tenant "<tenant-ID>" --allow-no-subscriptions
    az account show --sdk-auth
    ```

6. 检查生成的 JSON 对象。 你将在 GitHub 存储库中使用 JSON 对象创建包含你的操作的机密。 JSON 对象应具有以下属性：

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

## <a name="create-the-web-app-publish-profile"></a>创建 web 应用发布配置文件

### <a name="open-the-create-web-app-blade"></a>打开 "创建 web 应用" 边栏选项卡

1. 登录到 Azure Stack 中心门户。
1. 选择“创建资源” > “Web + 移动” > “Web 应用”。
    ![在 Azure Stack Hub 中创建 web 应用](\media\ci-cd-github-action-webapp\create-web-app.png)
### <a name="to-create-your-web-app"></a>创建 web 应用

1. 选择 **订阅**。
1. 创建或选择 **资源组**。
1. 键入应用程序的 **名称** 。 应用名称将显示在应用的 URL 中，例如 `yourappname.appservice.<region>.<FQDN>`
1. 选择应用的 **运行时堆栈** 。 运行时必须匹配用于发布配置文件的工作流。
1. 选择将承载运行时和应用的 **操作系统** (操作系统) 。
1. 为 Azure Stack 中心实例选择或键入 **区域** 。
1. 根据 Azure Stack 中心实例、区域和应用 OS 选择计划。
1. 选择“查看 + 创建”。
1. 查看你的 Web 应用。 选择“创建”。
    ![查看 Azure Stack 集线器中的 web 应用](\media\ci-cd-github-action-webapp\review-azure-stack-hub-web-app.png)
1. 选择“转到资源”。
    ![Azure Stack 中心获取发布配置文件](\media\ci-cd-github-action-webapp\get-azure-stack-hub-web-app-publish-profile.png)
1. 选择 " **获取发布配置文件**"。 你的发布配置文件将下载并命名为 `<yourappname>.PublishSettings` 。 此文件包含一个 XML，其中包含 web 应用的目标值。
1. 存储你的发布配置文件，以便你可以在为存储库创建机密时进行访问。

## <a name="add-your-secrets-to-the-repository"></a>将你的机密添加到存储库

你可以使用 GitHub 机密对要在操作中使用的敏感信息进行加密。 你将创建一个包含 SPN 的机密，并创建另一个机密来包含你的 web 应用发布配置文件，以便该操作可以登录到 Azure Stack 集线器实例，并将你的应用程序构建到 web 应用目标。

1. 打开或创建 GitHub 存储库。 如果你需要有关在 GitHub 中创建存储库的指南，可以查找 [GitHub 文档中的说明](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo)。
1. 选择“设置”。
1. 选择“机密”。
1. 选择“新建存储库机密”。
    ![添加你的 GitHub Actions 机密](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. 将你的机密命名为 `AZURE_CREDENTIALS`。
1. 粘贴表示你的 SPN 的 JSON 对象。
1. 选择“添加机密”。
1. 选择“新建存储库机密”。
1. 将你的机密命名为 `AZURE_WEBAPP_PUBLISH_PROFILE`。
1. `<yourappname>.PublishSettings`在文本编辑器中打开，然后将 XML 复制并粘贴到存储库机密。
1. 选择“添加机密”。

## <a name="add-a-runtime-workflow"></a>添加运行时工作流

1. 从表中为 web 应用运行时选择一个模板。

    | 运行时 | 模板 |
    | --- | --- |
    | DotNet | [dotnet. docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/asp.net-core-webapp-on-azure.yml) |
    | NodeJS | [docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/node.js-webapp-on-azure.yml) |
    | Java | [java_jar. docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-jar-webapp-on-azure.yml) |
    | Java | [java_war. docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-war-webapp-on-azure.yml) |
    | Python | [python. docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/python-webapp-on-azure.yml) |
    | PHP | [docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/php-webapp-on-azure.yml) |
    | Docker | [docker. docker-compose.override.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/docker-webapp-container-on-azure.yml) |

2. 将模板 GitHub 操作工作流目录放在你的项目存储库中： `.github/workflows/<runtime.yml>` 你的工作流目录将包含两个工作流。

## <a name="add-the-web-app-deploy-action"></a>添加 web 应用部署操作

使用本部分中的 yaml 创建第二个工作流。 在此示例中，你将部署一个 Python web 应用。 需要选择基于工作流的设置操作。 你可以在用于创建操作的步骤之后，查找用于为表中的各个运行时设置操作的引用、 [针对不同运行时的设置操作](#setup-actions-for-different-runtimes)。

### <a name="example-github-action-workflow"></a>GitHub 操作工作流示例

1. 打开你的 GitHub 存储库。 如果尚未添加 web 应用应用程序资源，请立即添加。 在此示例中，我使用的是 [Python Flask Hello World](https://github.com/Azure-Samples/python-docs-hello-world) 示例，我添加了 python `.gitignore` 、 `app.py` 和 `requirements.txt` 文件。

    ![将 Python Flask 与 Azure Stack 中心结合使用的示例存储库](\media\ci-cd-github-action-webapp\example-of-repo.png)
1. 选择“操作”。
1. 选择“新建工作流”。
    - 如果这是你的第一个工作流，请在“选择工作流模板”下选择“自己设置工作流” 。
    - 如果你已有工作流，请选择“新建工作流” > “自己设置工作流”。 

4. 在路径中，将文件命名为 `workflow.yml`。
5. 复制并粘贴工作流 yml。
    ```yaml  
    # .github/workflows/worfklow.yml
    on: push

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout Github Action' 
          uses: actions/checkout@master
    
        - name: Setup Python 3.6
          uses: actions/setup-node@v1
          with:
            python-version: '3.6'
        - name: 'create a virtual environment and install dependencies'
          run: |
            python3 -m venv .venv
            source .venv/bin/activate
            pip install -r requirements.txt
    
        - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with:
            app-name: <YOURAPPNAME>
            publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
    ```

6. 在 yaml 更新中 `<YOURAPPNAME>` ，应用程序名称为。
7. 选择“开始提交”。
8. 添加提交标题和可选的详细信息，然后选择“提交新文件”。


### <a name="setup-actions-for-different-runtimes"></a>针对不同运行时的设置操作

若要在特定的基于语言的环境中生成应用代码，请使用安装程序操作：

|  运行时 | 设置操作 |
|------------|---------|
| DotNet     | [设置 DotNet](https://github.com/actions/setup-dotnet) |
| NodeJS     | [设置节点](https://github.com/actions/setup-node) |
| Java     | [设置 Java  ](https://github.com/actions/setup-java) |
| Python     | [设置 Python ](https://github.com/actions/setup-python) |
| Docker | [docker-登录 ](https://github.com/Azure/docker-login) |

登录操作完成后，工作流中的下一组操作可以执行生成、标记和推送容器等任务。 有关详细信息，请参阅 [Azure Webapp 操作](https://github.com/marketplace/actions/azure-webapp)的文档。
## <a name="trigger-your-deployment"></a>触发部署

当操作运行时，验证它是否已成功运行。

1. 打开你的 GitHub 存储库。 可以通过推送到存储库来触发工作流。
1. 选择“操作”。
1. 在“所有工作流”下选择提交的名称。 这两个工作流都记录了其状态。
    ![查看 GitHub 操作的状态](\media\ci-cd-github-action-webapp\workflow-success.png)
1. 为部署选择作业的名称 `.github/workflows/workflow.yml` 。
1. 展开部分以查看工作流操作的返回值。 查找已部署的 web 应用的 URL。
    ![查找 Azure Stack 中心 web 应用 URL](\media\ci-cd-github-action-webapp\review-cli-log-and-get-url.png)
1. 打开 web 浏览器并加载 URL。
## <a name="next-steps"></a>后续步骤

- 在 [GitHub 市场](https://github.com/marketplace)中查找更多操作。
- 了解 [Azure Stack Hub 的常见部署](azure-stack-dev-start-deploy-app.md)  
- 了解[在 Azure Stack Hub 中使用 Azure 资源管理器模板](azure-stack-arm-templates.md)  
- 查看 DevOps 混合云模式：[DevOps 模式](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline)
