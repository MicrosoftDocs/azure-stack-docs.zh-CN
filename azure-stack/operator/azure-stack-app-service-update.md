---
title: Azure Stack 中心更新 Azure App Service
description: 了解如何在 Azure Stack 中心更新 Azure App Service。
author: BryanLa
ms.topic: article
ms.date: 01/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: 07cf0762a87fbf0cea490d246e6e31e003fd7275
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568599"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心更新 Azure App Service

> [!IMPORTANT]
> 在部署 Azure App Service 1.8 之前，将1910更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

本文介绍如何升级在连接 internet Azure Stack 中心环境中部署的[Azure App Service 资源提供程序](azure-stack-app-service-overview.md)。

> [!IMPORTANT]
> 在运行升级之前，请确保已完成[Azure Stack 中心的 Azure App Service 部署](azure-stack-app-service-deploy.md)。 还应阅读1.8 版本随附的[发行说明](azure-stack-app-service-release-notes-update-eight.md)，以便您可以了解有关新功能、修复程序以及可能影响部署的任何已知问题。

## <a name="run-the-azure-app-service-resource-provider-installer"></a>运行 Azure App Service 资源提供程序安装程序

在此过程中，升级将：

* 检测预先 Azure App Service 的部署。
* 准备要部署的所有更新包以及所有 OSS 库的新版本。
* 上传到存储。
* 升级所有 Azure App Service 角色（控制器、管理、前端、发布者和辅助角色）。
* 更新 Azure App Service 规模集定义。
* 更新 Azure App Service 资源提供程序清单。

> [!IMPORTANT]
> Azure App Service 安装程序必须在可访问 Azure Stack 中心管理员 Azure 资源管理器终结点的计算机上运行。

若要在 Azure Stack Hub 上升级 Azure App Service 部署，请执行以下步骤：

1. 下载[Azure App Service 安装程序](https://aka.ms/appsvcupdate8installer)。

2. 以管理员身份运行 appservice。

    ![Azure App Service 安装程序][1]

3. 单击 "**部署 Azure App Service 或升级到最新版本。**

4. 查看并接受 Microsoft 软件许可条款，然后单击 "**下一步**"。

5. 查看并接受第三方许可条款，然后单击 "**下一步**"。

6. 请确保 Azure Stack 中心 Azure 资源管理器终结点和 Active Directory 租户信息正确。 如果在 ASDK 部署过程中使用了默认设置，则可以在此处接受默认值。 但是，如果您在部署 Azure Stack 集线器时自定义了选项，则必须在此窗口中编辑这些值。 例如，如果使用域后缀*mycloud.com*，则 Azure Stack 中心 Azure 资源管理器终结点必须更改为*management.region.mycloud.com*。 确认信息后，单击 "**下一步**"。

    ![Azure Stack 中心云信息][2]

7. 在下一页上：

    1. 选择要使用的连接方法-**凭据**或**服务主体**
        - **凭据**
            - 如果使用 Azure Active Directory （Azure AD），请输入在部署 Azure Stack 中心时提供的 Azure AD 管理员帐户和密码。 选择“连接”。
            - 如果使用 Active Directory 联合身份验证服务（AD FS），请提供管理员帐户。 例如，cloudadmin@azurestack.local 。 输入密码，然后选择 "**连接**"。
        - **Service Principal**
            - 你使用的服务主体**必须**对**默认提供程序订阅**具有**所有者**权限
            - 提供**服务主体 ID**、**证书文件**和**密码**，然后选择 "**连接**"。

    1. 在**Azure Stack 中心订阅**"中，选择**默认提供程序订阅**。    **必须**在**默认提供程序订阅**中部署 Azure Stack 中心的 Azure App Service。

    1. 在**Azure Stack 集线器位置**，选择与要部署到的区域相对应的位置。 例如，如果要部署到 ASDK，请选择 "**本地**"。

    1. 如果检测到现有 Azure App Service 部署，则将填充资源组和存储帐户，且该帐户不可用。

      ![检测到 Azure App Service 安装][3]

8. 在 "摘要" 页上：
   1. 验证所做的选择。 若要进行更改，请使用**前面**的按钮来访问以前的页面。
   2. 如果配置正确，请选中该复选框。
   3. 若要开始升级，请单击 "**下一步**"。

       ![Azure App Service 升级摘要][4]

9. 升级进度页面：
    1. 跟踪升级进度。 Azure Stack 中心的 Azure App Service 升级持续时间取决于部署的角色实例的数量。
    2. 升级成功完成后，单击 "**退出**"。

        ![Azure App Service 升级进度][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>后续步骤

准备 Azure Stack 中心的 Azure App Service 的其他管理操作：

* [规划额外的容量](azure-stack-app-service-capacity-planning.md)
* [添加其他容量](azure-stack-app-service-add-worker-roles.md)
