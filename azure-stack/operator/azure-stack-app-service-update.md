---
title: 更新 Azure Stack Hub 上的 Azure 应用服务
description: 了解如何更新 Azure Stack Hub 上的 Azure 应用服务。
author: BryanLa
ms.topic: article
ms.date: 05/05/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 5dc8599a62c4b9e10d603bf2f02f18bf5024e203
ms.sourcegitcommit: edb60c948b445537e9411d6261c6c78359b71d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91810654"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>更新 Azure Stack Hub 上的 Azure 应用服务

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

::: zone pivot="state-connected"
在本文中，你将了解如何升级在连接到 Internet 的 Azure Stack Hub 环境中部署的 [Azure 应用服务资源提供程序](azure-stack-app-service-overview.md)。

> [!IMPORTANT]
> 在运行升级之前，必须完成[在 Azure Stack Hub 上部署 Azure 应用服务](azure-stack-app-service-deploy.md)。 

## <a name="run-the-azure-app-service-resource-provider-installer"></a>运行 Azure 应用服务资源提供程序安装程序

在此过程中，升级操作将会：

* 检测以前部署的 Azure 应用服务。
* 准备要部署的所有 OSS 库的所有更新包和新版本。
* 上传到存储。
* 升级所有 Azure 应用服务角色（控制器、管理、前端、发布者和辅助角色）。
* 更新 Azure 应用服务规模集定义。
* 更新 Azure 应用服务资源提供程序清单。

> [!IMPORTANT]
> Azure 应用服务安装程序必须在可访问“Azure Stack Hub 管理”Azure 资源管理器终结点的计算机上运行。

若要升级 Azure Stack Hub 上的 Azure 应用服务部署，请按照以下步骤操作：

1. 下载 [Azure 应用服务安装程序](https://aka.ms/appsvcupdateQ2installer)。

2. 以管理员身份运行 appservice.exe。

    ![显示如何在应用服务安装程序中启动部署或升级过程的屏幕截图。][1]

3. 单击“部署 Azure 应用服务或升级到最新版本”。****

4. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”****。

5. 查看并接受第三方许可条款，然后单击“下一步”****。

6. 确保 Azure Stack Hub Azure 资源管理器终结点和 Active Directory 租户信息正确。 如果在 ASDK 部署过程中使用了默认设置，则此处可以接受默认值。 但是，如果在部署 Azure Stack Hub 时自定义了选项，则必须编辑此窗口中的值。 例如，如果使用域后缀 *mycloud.com*，则必须将 Azure Stack Hub Azure 资源管理器终结点更改为 *management.region.mycloud.com*。 确认信息后，单击“下一步”****。

    ![屏幕截图，显示在应用服务安装程序中配置 ARM 终结点的位置。][2]

7. 在下一页上执行以下操作：

    1. 选择要使用的连接方法 - “凭据”或“服务主体”**** ****
        - **凭据**
            - 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack Hub 时提供的 Azure AD 管理员帐户和密码。 选择“连接” ****。
            - 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，cloudadmin@azurestack.local。 输入密码，然后选择“连接”****。
        - **服务主体**
            - 使用的服务主体必须对“默认提供程序订阅”拥有“所有者”权限**** **** ****
            - 提供“服务主体 ID”、“证书文件”和“密码”，然后选择“连接”**** **** **** ****。

    1. 在“Azure Stack Hub 订阅”中，选择“默认提供程序订阅”。**** ****    Azure Stack Hub 上的 Azure 应用服务**必须**部署在**默认提供程序订阅**中。

    1. 在“Azure Stack Hub 位置”**** 中，选择要部署到的区域所对应的位置。 例如，若要部署到 ASDK，请选择“本地”。****

    1. 如果检测到现有的 Azure 应用服务部署，则资源组和存储帐户会被填充并不可用。

      ![屏幕截图，显示在应用服务安装程序中指定 Azure Stack 中心订阅信息的位置。][3]

8. 在摘要页上执行以下操作：
   1. 验证所做的选择。 若要进行更改，请使用“上一步”**** 按钮访问前面的页面。
   2. 如果配置正确，则选中此复选框。
   3. 若要开始升级，请单击“下一步”。****

       ![显示安装程序中的应用服务升级摘要的屏幕截图。][4]

9. 升级进度页：
    1. 跟踪升级进度。 Azure Stack Hub 上的 Azure 应用服务升级持续时间取决于部署的角色实例数目。
    2. 升级成功完成后，单击“退出”。****

        ![显示应用服务安装程序中的部署进度的屏幕截图。][5]
::: zone-end

::: zone pivot="state-disconnected"
在本文中，你会了解如何升级部署在处于以下状态的 Azure Stack Hub 环境中的 [Azure 应用服务资源提供程序](azure-stack-app-service-overview.md)：

* 未连接到 Internet
* 受 Active Directory 联合身份验证服务 (AD FS) 保护。

> [!IMPORTANT]
> 在运行升级之前，必须已完成[在断开连接的环境中的 Azure Stack Hub 上部署 Azure 应用服务](./azure-stack-app-service-deploy.md?pivots=state-disconnected&view=azs-2002)。 

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

若要升级 Azure Stack Hub 环境中的应用服务资源提供程序，必须完成以下任务：

1. 下载 [Azure 应用服务安装程序](https://aka.ms/appsvcupdate8installer)。
2. 创建离线升级包。
3. 运行应用服务安装程序 (appservice.exe) 并完成升级。

在此过程中，升级操作将会：

* 检测以前部署的应用服务
* 上传到存储
* 升级所有应用服务角色（控制器、管理、前端、发布者和辅助角色）
* 更新应用服务规模集定义
* 更新应用服务资源提供程序清单

## <a name="create-an-offline-upgrade-package"></a>创建离线升级包

若要在离线环境中升级应用服务，必须先在连接到 Internet 的计算机上创建离线升级包。

1. 以管理员身份运行 appservice.exe

    ![显示如何在断开连接环境中开始升级的屏幕截图。][11]

2. 单击“高级”**** > ****“创建离线包”

    ![演示如何在应用服务安装程序中创建脱机包的屏幕截图。][12]

3. Azure 应用服务安装程序将创建离线升级包并显示其路径。  可以单击“打开文件夹”，在文件资源管理器中打开该文件夹。****

4. 将安装程序 (AppService.exe) 和脱机安装包复制到已连接 Azure Stack Hub 的计算机。

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>完成 Azure Stack Hub 上的应用服务的升级

> [!IMPORTANT]
> Azure 应用服务安装程序必须在可访问“Azure Stack Hub 管理员”Azure 资源管理器终结点的计算机上运行。

1. 以管理员身份运行 appservice.exe。

    ![显示如何开始升级的屏幕截图。][11]

2. 单击“高级”**** > ****“完成离线安装或升级”。

    ![演示如何在应用服务安装程序中完成脱机安装或升级的屏幕截图。][12]

3. 浏览到前面创建的离线升级包所在的位置，单击“下一步”。****

4. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”****。

5. 查看并接受第三方许可条款，然后单击“下一步”****。

6. 确保 Azure Stack Hub Azure 资源管理器终结点和 Active Directory 租户信息正确。 如果在 Azure Stack 开发工具包部署过程中使用了默认设置，可以接受此处的默认值。 但是，如果在部署 Azure Stack Hub 时自定义了选项，则必须编辑此窗口中的值。 例如，如果使用域后缀 *mycloud.com*，则必须将 Azure Stack Hub Azure 资源管理器终结点更改为 *management.region.mycloud.com*。 确认信息后，单击“下一步”****。

    ![显示在安装程序中配置 ARM 终结点的位置的屏幕截图。][13]

7. 在下一页上执行以下操作：

   1. 选择要使用的连接方法 - “凭据”或“服务主体”**** ****
        - **凭据**
            - 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack Hub 时提供的 Azure AD 管理员帐户和密码。 选择“连接” ****。
            - 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，cloudadmin@azurestack.local。 输入密码，然后选择“连接”****。
        - **服务主体**
            - 使用的服务主体必须对“默认提供程序订阅”拥有“所有者”权限**** **** ****
            - 提供“服务主体 ID”、“证书文件”和“密码”，然后选择“连接”**** **** **** ****。

   1. 在“Azure Stack Hub 订阅”中，选择“默认提供程序订阅”。**** ****  Azure Stack Hub 上的 Azure 应用服务**必须**部署在**默认提供程序订阅**中。

   1. 在“Azure Stack Hub 位置”**** 中，选择要部署到的区域所对应的位置。 例如，若要部署到 ASDK，请选择“本地”。****
   
   1. 如果检测到现有的应用服务部署，则资源组和存储帐户将被填充并灰显。

      ![屏幕截图，显示在安装程序中配置 Azure Stack 中心订阅的位置。][14]
8. 在摘要页上执行以下操作：
   1. 验证所做的选择。 若要进行更改，请使用“上一步”**** 按钮访问前面的页面。
   2. 如果配置正确，则选中此复选框。
   3. 若要开始升级，请单击“下一步”。****

       ![屏幕截图，显示安装程序中收集的信息的摘要。][15]

9. 升级进度页：
    1. 跟踪升级进度。 Azure Stack Hub 上的应用服务升级持续时间取决于部署的角色实例数目。
    2. 升级成功完成后，单击“退出”。****

        ![显示升级已成功完成的屏幕截图。][16]
::: zone-end

## <a name="next-steps"></a>后续步骤

准备 Azure Stack Hub 上的 Azure 应用服务的其他管理操作：

* [规划更多容量](azure-stack-app-service-capacity-planning.md)
* [添加更多容量](azure-stack-app-service-add-worker-roles.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

[11]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[12]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[13]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[14]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[15]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[16]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png
