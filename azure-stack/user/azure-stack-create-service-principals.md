---
title: 创建 Azure Stack 的服务主体 | Microsoft Docs
description: 介绍了如何创建服务主体，并在 Azure 资源管理器中将此服务主体与基于角色的访问控制配合使用以管理对资源的访问权限。
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3fc50d068eb07d7da2d55d855b2ea8ea758ca6b1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300611"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>通过创建服务主体向应用程序授予对 Azure Stack 资源的访问权限

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过创建使用 Azure 资源管理器的服务主体向应用程序授予对 Azure Stack 资源的访问权限。 服务主体允许使用[基于角色的访问控制](azure-stack-manage-permissions.md)来委派特定权限。

应当为应用程序使用服务主体，这是一种最佳做法。 与使用自己的凭据运行应用相比，使用服务主体更优，原因如下：

* 可以向服务主体分配不同于自己的帐户权限的权限。 通常情况下，服务主体的权限仅限于应用需执行的确切操作。
* 角色或职责变化时，无需更改应用的凭据。
* 运行无人参与的脚本时，可以使用证书自动执行身份验证。

## <a name="example-scenario"></a>示例方案

你有一个需要使用 Azure 资源管理器来清点 Azure 资源的配置管理应用。 可以创建一个服务主体并将其分配到“读者”角色。 此角色向该应用授予对 Azure 资源的只读访问权限。

## <a name="getting-started"></a>入门

使用本文中的步骤作为指南来完成以下任务：

* 为应用创建服务主体。
* 注册应用并创建身份验证密钥。
* 将应用分配到某个角色。

你为 Azure Stack 配置 Active Directory 的方式决定了你如何创建服务主体。 选择以下选项之一：

* 创建适用于 [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) 的服务主体。
* 创建适用于 [Active Directory 联合身份验证服务 (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs) 的服务主体。

将服务主体分配到角色的步骤对于 Azure AD 和 AD FS 是相同的。 创建服务主体后，可以通过将其分配到某个角色来[委派权限](azure-stack-create-service-principals.md)。

## <a name="create-service-principal-for-azure-ad"></a>为 Azure AD 创建服务主体

如果你的 Azure Stack 使用 Azure AD 作为标识存储，则可以通过 Azure 门户使用与 Azure 中相同的步骤来创建服务主体。

> [!NOTE]
> 在开始创建服务主体之前，请检查你是否具有[所需的 Azure AD 权限](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="create-service-principal"></a>创建服务主体

若要为应用程序创建服务主体，请执行以下操作：

1. 通过 [Azure 门户](https://portal.azure.com)登录到 Azure 帐户。
2. 选择**Azure Active Directory** > **应用注册** > **新注册**。
3. 提供一个名称。
4. 选择**支持的帐户类型**。
5.  添加应用程序的 URI。 选择**Web**为你想要创建的应用程序的类型。 设置这些值后, 选择**注册**。

### <a name="get-credentials"></a>获取凭据

以编程方式登录时，需要使用应用程序的 ID 和身份验证密钥。 若要获取这些值：

1. 从 Active Directory 中的“应用注册”，选择应用程序。

2. 复制“应用程序 ID”并将其存储在应用程序代码中。 在引用“应用程序 ID”时，示例应用程序中的应用程序使用“客户端 ID”。

     ![应用程序的应用程序 ID](./media/azure-stack-create-service-principals/image12.png)
3. 若要生成身份验证密钥，请选择**证书和机密**。

4. 选择“新建客户端机密”。

5. 提供密钥的说明，选择持续时间的键，然后选择**添加**。 

6. 完成后，显示机密的值。 请记下此值，因为以后无法检索密钥。 将密钥值存储在应用程序可检索的位置。

![有关已保存的密钥的密钥值警告。](./media/azure-stack-create-service-principals/image15.png)

最后一步是[将应用程序分配到某个角色](azure-stack-create-service-principals.md)。

## <a name="create-service-principal-for-ad-fs"></a>为 AD FS 创建服务主体

如果部署了使用 AD FS 作为标识存储的 Azure Stack，则可以使用 PowerShell 执行以下任务：

* 创建服务主体。
* 将服务主体分配到某个角色。
* 使用服务主体的标识进行登录。

有关如何创建服务主体的详细信息，请参阅[为 AD FS 创建服务主体](../operator/azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)。

## <a name="assign-the-service-principal-to-a-role"></a>将服务主体分配到某个角色

要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](/azure/role-based-access-control/built-in-roles)。

> [!NOTE]
> 可以在订阅、资源组或资源级别设置角色的作用域。 较低级别的作用域将继承权限。 例如，如果应用具有某个资源组的“读者”角色，则意味着该应用可以读取该资源组中的任何资源。

请使用以下步骤作为指南来为服务主体分配角色。

1. 在 Azure Stack 门户中，导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”。

2. 选择要将应用程序分配到的订阅。 在此示例中，该订阅是 Visual Studio Enterprise。

     ![选择 Visual Studio Enterprise 订阅用于分配](./media/azure-stack-create-service-principals/image16.png)

3. 选择该订阅的“访问控制(标识和访问管理)”。

4. 选择“添加角色分配”。

5. 选择要分配到应用程序的角色。

6. 搜索你的应用程序，并选择它。

7. 选择“确定”完成角色分配。 该应用程序将显示在分配到该作用域的某个角色的用户列表中。

现在，你已创建了服务主体并分配了角色，你的应用程序可以访问 Azure Stack 资源了。

## <a name="next-steps"></a>后续步骤

[管理用户权限](azure-stack-manage-permissions.md)
