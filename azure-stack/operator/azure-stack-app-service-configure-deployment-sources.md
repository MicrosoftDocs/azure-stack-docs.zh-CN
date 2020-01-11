---
title: 在 Azure Stack Hub 上配置应用服务的部署源 |Microsoft Docs
description: 了解如何在 Azure Stack 中心为应用服务配置部署源（Git、GitHub、BitBucket、DropBox 和 OneDrive）。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0115e726e8922b94eae437cb76e23f4e77199d97
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880900"
---
# <a name="configure-deployment-sources-for-app-services-on-azure-stack-hub"></a>在 Azure Stack 中心为应用服务配置部署源

Azure Stack 集线器上的应用服务支持从多个源代码管理提供程序进行按需部署。 此功能使应用程序开发人员可以直接从其源代码管理存储库部署。 如果用户想要将应用服务配置为连接到其存储库，云操作员必须首先在 Azure Stack 中心和源代码管理提供程序上配置应用服务之间的集成。  

除了本地 Git 以外，还支持以下源代码管理提供程序：

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>在应用服务管理中查看部署源

1. 以服务管理员身份登录到 Azure Stack 中心管理门户（ https://adminportal.local.azurestack.external) 。
2. 浏览到**所有服务**并选择**应用服务**。
    ![应用服务资源提供程序管理][1]
3. 单击“源代码管理配置”。 你可以看到所有已配置的部署源的列表。
    ![应用服务资源提供程序管理中的源代码管理配置][2]

## <a name="configure-github"></a>配置 GitHub

若要完成此任务，必须具有 GitHub 帐户。 你可能想要为你的组织使用帐户，而不是个人帐户。

1. 登录到 GitHub，浏览到 https://www.github.com/settings/developers ，然后单击 "**注册新应用程序**"。
    ![GitHub-注册新应用程序][3]
2. 输入**应用程序名称**。 例如， **Azure Stack 集线器上的应用服务**。
3. 输入“主页 URL”。 主页 URL 必须是 Azure Stack 中心门户地址。 例如， https://portal.local.azurestack.external 。
4. 输入**应用程序说明**。
5. 输入“授权回调 URL”。 在默认 Azure Stack 中心部署中，URL 的格式为 https://portal.local.azurestack.external/TokenAuthorize 。 如果你在其他域下运行，请将你的域名切换为 test-azurestack。
6. 单击“注册应用程序”。 将显示一个页面，其中列出了应用的**客户端 ID**和**客户端密码**。
    ![GitHub - 已完成应用程序注册][5]
7. 在新的浏览器选项卡或窗口中，登录到 Azure Stack 中心管理门户（ https://adminportal.local.azurestack.external) 为服务管理员。
8. 浏览到**资源提供程序**，并选择 "**应用服务资源提供程序管理员**"。
9. 单击“源代码管理配置”。
10. 将**客户端 ID**和**客户端密码**复制并粘贴到 GitHub 的相应输入框中。
11. 单击“ **保存**”。

## <a name="configure-bitbucket"></a>配置 BitBucket

必须具有 BitBucket 帐户才能完成此任务。 你可能想要为你的组织使用帐户，而不是个人帐户。

1. 登录到 BitBucket 并浏览到帐户下的 "**集成**"。
    ![BitBucket 仪表板-集成][7]
2. 单击 "访问管理" 下面的 " **OAuth** "，并**添加使用者**。
    ![BitBucket 添加 OAuth 使用者][8]
3. 输入使用者的**名称**。 例如， **Azure Stack 集线器上的应用服务**。
4. 输入应用的**描述**。
5. 输入“回调 URL”。 在默认 Azure Stack 中心部署中，回调 URL 的格式为 https://portal.local.azurestack.external/TokenAuthorize 。 如果在不同的域下运行，请将域名替换为 "test-azurestack"。 若要成功进行 BitBucket 集成，URL 必须遵循此处列出的大小写。
6. 输入**URL**。 此 URL 应为 Azure Stack 中心门户 URL。 例如， https://portal.local.azurestack.external 。
7. 选择所需**权限**：
    - **存储库**：*读取*
    - **Webhook**：*读取和写入*
8. 单击“ **保存**”。 现在，可以在**OAuth 使用者**下看到此新应用以及**密钥**和**机密**。
    ![BitBucket 应用程序列表][9]
9.  在新的浏览器选项卡或窗口中，登录到 Azure Stack 中心管理门户（ https://adminportal.local.azurestack.external) 为服务管理员。
10.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
11. 单击“源代码管理配置”。
12. 将该**密钥**复制并粘贴到 "**客户端 ID** " 输入框中，并将**密码**复制到 BitBucket 的 "**客户端密码**" 输入框中。
13. 单击“ **保存**”。

## <a name="configure-onedrive"></a>配置 OneDrive

必须已将 Microsoft 帐户链接到 OneDrive 帐户才能完成此任务。  你可能想要为你的组织使用帐户，而不是个人帐户。

> [!NOTE]
> 当前不支持 OneDrive for business 帐户。

1. 浏览到 https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm 并使用您的 Microsoft 帐户登录。
2. 在 **"我的应用程序**" 下，单击 "**添加应用**"。
![OneDrive 应用程序][10]
3. 输入新应用注册的**名称**：**在 Azure Stack 集线器上输入应用服务**，然后单击 "**创建应用程序**"。
4. 下一屏幕中列出了新应用的属性。 将**应用程序 ID**保存到某个临时位置。
![OneDrive 应用程序属性][11]
5. 在“应用程序密码”下，单击“生成新密码”。 记下**生成的新密码**。 此密码是你的应用程序密码，单击 **"确定"** 后将无法检索它。
6. 在 "**平台**" 下，单击 "**添加平台**"，然后选择 " **Web**"。
7. 输入“重定向 URI”。 在默认 Azure Stack 中心部署中，重定向 URI 的格式为 https://portal.local.azurestack.external/TokenAuthorize 。 如果在不同的域下运行，请将域名切换为 test-azurestack。
![OneDrive 应用程序-添加 Web 平台][12]
8.  - **委托权限**添加**Microsoft Graph 权限**。
    - **Files.ReadWrite.AppFolder**
    - **用户.读取**  
      ![OneDrive 应用程序 - Graph 权限][13]
9. 单击“ **保存**”。
10.  在新的浏览器选项卡或窗口中，登录到 Azure Stack 中心管理门户（ https://adminportal.local.azurestack.external) 为服务管理员。
11.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
12. 单击“源代码管理配置”。
13. 将**应用程序 id**复制并粘贴到 "**客户端 id** " 输入框中，并将**密码**粘贴到 OneDrive 的 "**客户端密码**" 输入框中。
14. 单击“ **保存**”。

## <a name="configure-dropbox"></a>配置 DropBox

> [!NOTE]
> 必须有一个 DropBox 帐户才能完成此任务。 你可能想要为你的组织使用帐户，而不是个人帐户。

1. 浏览到 https://www.dropbox.com/developers/apps 并使用 DropBox 帐户凭据登录。
2. 单击 "**创建应用**"。

    ![Dropbox 应用][14]

3. 选择**DROPBOX API**。
4. 将访问级别设置为 "**应用" 文件夹**。
5. 输入应用的**名称**。
![Dropbox 应用程序注册][15]
6. 单击“创建应用”。 将显示一个页面，其中列出了应用的设置，包括**应用密钥**和**应用密码**。
7. 请确保将**应用文件夹名称**设置为**Azure Stack 集线器上的应用服务**。
8. 设置**OAuth 2 重定向 URI** ，然后单击 "**添加**"。 在默认 Azure Stack 中心部署中，重定向 URI 的格式为 https://portal.local.azurestack.external/TokenAuthorize 。 如果在不同的域下运行，请将域切换为 test-azurestack。
![Dropbox 应用程序配置][16]
9.  在新的浏览器选项卡或窗口中，登录到 Azure Stack 中心管理门户（ https://adminportal.local.azurestack.external) 为服务管理员。
10.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
11. 单击“源代码管理配置”。
12. 将**应用程序密钥**复制并粘贴到 "**客户端 ID** " 输入框中，并将应用密钥粘贴到 DropBox 的 " **客户端密码**" 输入框中。
13. 单击“ **保存**”。

## <a name="next-steps"></a>后续步骤

用户现在可以使用部署源进行[持续部署](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)、[本地 Git 部署](https://docs.microsoft.com/azure/app-service/deploy-local-git)和[云文件夹同步](https://docs.microsoft.com/azure/app-service/deploy-content-sync)等任务。

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
