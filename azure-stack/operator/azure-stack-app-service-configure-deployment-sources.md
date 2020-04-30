---
title: 为 Azure Stack Hub 上的应用服务配置部署源
description: 了解如何为 Azure Stack Hub 上的应用服务配置部署源（Git、GitHub、BitBucket、DropBox 和 OneDrive）。
author: bryanla
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 1829d56488501e03a18f27e87f1fd0742626e3be
ms.sourcegitcommit: 3fd4a38dc8446e0cdb97d51a0abce96280e2f7b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580151"
---
# <a name="configure-deployment-sources-for-app-services-on-azure-stack-hub"></a>为 Azure Stack Hub 上的应用服务配置部署源

Azure Stack Hub 上的应用服务支持从多个源代码管理提供程序执行按需部署。 此功能使应用程序开发人员可以直接从其源代码管理存储库部署。 如果用户想要将应用服务配置为连接到其存储库，云操作员必须先在 Azure Stack Hub 上的应用服务与源代码管理提供程序之间配置集成。  

除了本地 Git 以外，还支持以下源代码管理提供程序：

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>在应用服务管理中查看部署源

1. 以服务管理员身份登录到 Azure Stack 中心管理员门户。
2. 浏览到“所有服务”  ，然后选择“应用服务”  。

    ![应用服务资源提供程序管理][1]

3. 选择 "**源代码管理配置**"。 可以看到所有已配置的部署源的列表。

    ![应用服务资源提供程序管理源代码管理配置][2]

## <a name="configure-github"></a>配置 GitHub

必须具有 GitHub 帐户才能完成此任务。 建议使用组织帐户，而不是个人帐户。

1. 登录到 GitHub，中转到https://www.github.com/settings/developers，然后选择 "**注册新应用程序**"。

    ![GitHub - 注册新应用程序][3]

2. 输入**应用程序名称**。 例如，**Azure Stack Hub 上的应用服务**。
3. 输入“主页 URL”。  主页 URL 必须是 Azure Stack Hub 门户地址。 例如， `https://portal.<region>.<FQDN>` 。 有关 Azure Stack 中心完全限定的域名（FQDN）的详细信息，请参阅[Azure Stack 中心 DNS 命名空间](azure-stack-integrate-dns.md#azure-stack-hub-dns-namespace)。
4. 输入**应用程序说明**。
5. 输入“授权回调 URL”。**** 在默认 Azure Stack 中心部署中，URL 的格式`https://portal.<region>.<FQDN>/TokenAuthorize`为。 
6. 选择 "**注册应用程序**"。 此时会显示一个页面，其中列出了应用的“客户端 ID”和“客户端机密”。  

    ![GitHub - 已完成应用程序注册][5]

7. 在新的浏览器选项卡或窗口中，以服务管理员身份登录到 Azure Stack 中心管理员门户。
8. 请访问 "**资源提供程序**" 并选择 "**应用服务资源提供程序管理员**"。
9. 选择 "**源代码管理配置**"。
10. 将“客户端 ID”和“客户端机密”复制并粘贴到 GitHub 的相应输入框中。  
11. 选择“保存”。 

## <a name="configure-bitbucket"></a>配置 BitBucket

必须具有 BitBucket 帐户才能完成此任务。 建议使用组织帐户，而不是个人帐户。

1. 登录到 BitBucket 并中转到你的帐户下的 "**集成**"。

    ![BitBucket 仪表板 - 集成][7]

2. 选择 "访问管理" 下面的 " **OAuth** " 并**添加使用者**。

    ![BitBucket - 添加 OAuth 使用者][8]

3. 输入使用者的**名称**。 例如，**Azure Stack Hub 上的应用服务**。
4. 为应用输入**说明**。
5. 输入“回调 URL”。  在默认的 Azure Stack Hub 部署中，该回调 URL 采用 `https://portal.<region>.<FQDN>/TokenAuthorize` 格式。 要使 BitBucket 集成成功，该 URL 的大小写必须与此处所列相同。
6. 输入 **URL**。 此 URL 应为 Azure Stack 中心门户 URL。 例如，`https://portal.<region>.<FQDN>` 。
7. 选择所需**权限**：

    - **存储库**：*读取*
    - **Webhook**：*读取和写入*

8. 选择“保存”。  现在，“OAuth 使用者”下面会显示此新应用以及**密钥**和**机密**。****

    ![BitBucket 应用程序列表][9]

9. 在新的浏览器选项卡或窗口中，以服务管理员身份登录到 Azure Stack 中心管理员门户。
10. 请访问 "**资源提供程序**" 并选择 "**应用服务资源提供程序管理员**"。
11. 选择 "**源代码管理配置**"。
12. 将该**密钥**复制并粘贴到 "**客户端 ID** " 输入框中，并将**密码**复制到 BitBucket 的 "**客户端密码**" 输入框中。
13. 选择“保存”。 

## <a name="configure-onedrive"></a>配置 OneDrive

若要完成此任务，必须将 Microsoft 帐户链接到 OneDrive 帐户。 建议使用组织帐户，而不是个人帐户。

> [!NOTE]
> 当前不支持 OneDrive for business 帐户。

1. 使用 Microsoft 帐户https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm登录并登录。
2. 在 **"我的应用程序**" 下，选择 "**添加应用**"。

    ![OneDrive 应用程序][10]

3. 输入新应用注册的**名称**：**在 Azure Stack 集线器上输入应用服务**，然后选择 "**创建应用程序**"。
4. 下一屏幕列出了新应用的属性。 将**应用程序 ID**保存到临时位置。

    ![OneDrive 应用程序属性][11]

5. 在 "**应用程序机密**" 下，选择 "**生成新密码**"。 记下**生成的新密码**。 此密码是你的应用程序机密，你选择 **"确定"** 后将无法检索它。
6. 在 "**平台**" 下，选择 "**添加平台**"，然后选择 " **Web**"。
7. 输入“重定向 URI”。**** 在默认 Azure Stack 中心部署中，重定向 URI 的格式`https://portal.<region>.<FQDN>/TokenAuthorize`为。

    ![OneDrive 应用程序 - 添加 Web 平台][12]

8. 添加**Microsoft Graph 权限** - **委托权限**。

    - **Files.ReadWrite.AppFolder**
    - **用户。读取** ![OneDrive 应用程序-Graph 权限][13]

9. 选择“保存”。 
10. 在新的浏览器选项卡或窗口中，以服务管理员身份登录到 Azure Stack 中心管理员门户。
11. 请访问 "**资源提供程序**" 并选择 "**应用服务资源提供程序管理员**"。
12. 选择 "**源代码管理配置**"。
13. 将**应用程序 id**复制并粘贴到 "**客户端 id** " 输入框中，并将**密码**粘贴到 OneDrive 的 "**客户端密码**" 输入框中。
14. 选择“保存”。 

## <a name="configure-dropbox"></a>配置 DropBox

> [!NOTE]
> 必须使用一个 DropBox 帐户来完成此任务。 建议使用组织帐户，而不是个人帐户。

1. 请参阅https://www.dropbox.com/developers/apps ，并使用 DropBox 帐户凭据登录。
2. 选择“创建应用”****。

    ![Dropbox 应用][14]

3. 选择**DROPBOX API**。
4. 将访问级别设置为 "**应用" 文件夹**。
5. 输入应用的**名称**。

    ![Dropbox 应用程序注册][15]

6. 选择 "**创建应用**"。 此时会出现一个页面，其中列出了应用的设置，包括**应用密钥**和**应用机密**。
7. 请确保将**应用文件夹名称**设置为**Azure Stack 集线器上的应用服务**。
8. 设置**OAuth 2 重定向 URI** ，然后选择 "**添加**"。 在默认 Azure Stack 中心部署中，重定向 URI 的格式`https://portal.<region>.<FQDN>/TokenAuthorize`为。

    ![Dropbox 应用程序配置][16]

9. 在新的浏览器选项卡或窗口中，以服务管理员身份登录到 Azure Stack 中心管理员门户。
10. 请访问 "**资源提供程序**" 并选择 "**应用服务资源提供程序管理员**"。
11. 选择 "**源代码管理配置**"。
12. 将**应用程序密钥**复制并粘贴到 "**客户端 ID** " 输入框中，并将应用密钥粘贴到 DropBox 的 " **App secret** **客户端密码**" 输入框中。
13. 选择“保存”。 

## <a name="next-steps"></a>后续步骤

用户现在可以使用部署源进行[持续部署](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)、[本地 Git 部署](https://docs.microsoft.com/azure/app-service/deploy-local-git)和[云文件夹同步](https://docs.microsoft.com/azure/app-service/deploy-content-sync)等操作。

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
