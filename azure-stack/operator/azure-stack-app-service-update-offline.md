---
title: 离线更新 Azure 应用服务 | Microsoft Docs
description: 有关离线更新 Azure Stack 上的 Azure 应用服务的详细指导
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/27/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.openlocfilehash: 53b45a6ac5ef8aef1e8d07b242303ed0f248f506
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64295111"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>离线更新 Azure Stack 上的 Azure 应用服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!IMPORTANT]
> 请将 1901 更新或更高版本应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包，然后部署 Azure 应用服务 1.5。 

遵循本文中的说明可以升级部署在处于以下状态的 Azure Stack 环境中的[应用服务资源提供程序](azure-stack-app-service-overview.md)：

* 未连接到 Internet
* 受 Active Directory 联合身份验证服务 (AD FS) 保护。

> [!IMPORTANT]
> 在运行升级之前，请确保已完成[部署 Azure Stack 上的 Azure 应用服务资源提供程序](azure-stack-app-service-deploy-offline.md)，并已阅读版本 1.5 随附的[发行说明](azure-stack-app-service-release-notes-update-five.md)，了解新功能、修补程序以及任何可能影响部署的已知问题。

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

若要升级 Azure Stack 环境中的应用服务资源提供程序，必须完成以下任务：

1. 下载[应用服务安装程序](https://aka.ms/appsvcupdate4installer)
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

    ![应用服务安装程序][1]

2. 单击“高级” > “创建离线包”

    ![应用服务安装程序高级设置][2]

3. 应用服务安装程序将创建离线升级包并显示其路径。  可以单击“打开文件夹”，在文件资源管理器中打开该文件夹。

4. 将安装程序 (AppService.exe) 和离线升级包复制到 Azure Stack 主机。

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>完成 Azure Stack 上的应用服务的升级

> [!IMPORTANT]
> 应用服务安装程序必须在可访问“Azure Stack 管理员”Azure 资源管理器终结点的计算机上运行。
>
>

1. 以管理员身份运行 appservice.exe。

    ![应用服务安装程序][1]

2. 单击“高级” > “完成离线安装或升级”。

    ![应用服务安装程序高级设置][2]

3. 浏览到前面创建的离线升级包所在的位置，单击“下一步”。

4. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”。

5. 查看并接受第三方许可条款，然后单击“下一步”。

6. 确保 Azure Stack Azure 资源管理器终结点和 Active Directory 租户信息正确。 如果在 Azure Stack 开发工具包部署过程中使用了默认设置，可以接受此处的默认值。 但是，如果在部署 Azure Stack 时自定义了选项，则必须编辑此窗口中的值。 例如，如果使用域后缀 *mycloud.com*，则必须将“Azure Stack”Azure 资源管理器终结点更改为 *management.region.mycloud.com*。 确认信息后，单击“下一步”。

    ![Azure Stack 云信息][3]

7. 在下一页上执行以下操作：

   1. 单击“Azure Stack 订阅”框旁边的“连接”按钮。
      * 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack 时提供的 Azure AD 管理员帐户和密码。 单击“登录”。
      * 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，_cloudadmin@azurestack.local_。 输入密码，并单击“登录”。
   2. 在“Azure Stack 订阅”框中，选择“默认提供商订阅”。
   3. 在“Azure Stack 位置”框中，选择要部署到的区域所对应的位置。 例如，如果要部署到 Azure Stack 开发工具包，请选择“本地”。
   4. 如果检测到现有的应用服务部署，则资源组和存储帐户将被填充并灰显。
   5. 按“下一步”查看升级摘要。

      ![检测到应用服务安装][4]

8. 在摘要页上执行以下操作：
   1. 验证所做的选择。 若要进行更改，请使用“上一步”按钮访问前面的页面。
   2. 如果配置正确，则选中此复选框。
   3. 若要开始升级，请单击“下一步”。

       ![应用服务升级摘要][5]

9. 升级进度页：
    1. 跟踪升级进度。 Azure Stack 上的应用服务升级持续时间取决于部署的角色实例数目。
    2. 升级成功完成后，单击“退出”。

        ![应用服务升级进度][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-offer-services-overview.md)。

* [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
