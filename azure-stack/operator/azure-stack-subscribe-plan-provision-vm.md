---
title: 在 Azure Stack Hub 中创建对产品/服务的订阅。
titleSuffix: Azure Stack
description: 了解如何在 Azure Stack Hub 中创建产品/服务的订阅。
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 5225772f94e5a28d03872d1764681e56f14a2cf4
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294634"
---
# <a name="create-subscriptions-to-offers-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建对产品/服务的订阅

[创建套餐](azure-stack-create-offer.md)后，用户需要订阅该套餐，然后才能使用它。 用户可以通过两种方式订阅产品/服务：

- 在管理员门户中以云操作员身份为用户创建订阅。 创建的订阅可以是针对公共套餐的，也可以是针对专用套餐的。
- 在使用用户门户时以租户用户身份订阅公共套餐。  

## <a name="create-a-subscription-as-a-cloud-operator"></a>以云操作员身份创建订阅

云操作员可以使用管理员门户为用户创建产品/服务的订阅。 可为你自己的目录租户的成员创建订阅。 启用[多租户](azure-stack-enable-multitenancy.md)时，还可以为其他目录租户中的用户创建订阅。

如果你不希望你的租户创建自己的订阅，请将你的产品/服务设置为专用，然后为你的租户创建订阅。 将 Azure Stack 中心与外部计费或服务目录系统集成时，这种方法很常见。

为用户创建订阅后，他们可以登录到用户门户，并查看他们是否订阅了产品/服务。  

### <a name="to-create-a-subscription-for-a-user"></a>为用户创建订阅

1. 在管理员门户中，请参阅**用户订阅。**
2. 选择 **添加** 。 在 "**新建用户订阅**" 下，输入以下信息：  

   - **显示名称**-用于标识显示为*用户订阅名称*的订阅的友好名称。
   - **用户**-指定此订阅的可用目录租户中的用户。 用户名显示为 "*所有者*"。  用户名的格式取决于你的标识解决方案。 例如：

     - **Azure AD：** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS：** `<user1>@<azurestack.local>`

   - **目录租户**-选择用户帐户所属的目录租户。 如果尚未启用多租户，则只能使用本地目录租户。

3. 选择 "**产品/服务**"。 在 "**产品/服务**" 下，为此订阅选择产品/**服务**。 由于你要为用户创建订阅，因此请选择 "**专用**" 作为辅助功能状态。

4. 选择 "**创建**" 以创建订阅。 新订阅将显示在**用户订阅**下。 当用户登录到用户门户时，他们可以查看订阅详细信息。

### <a name="to-make-an-add-on-plan-available"></a>激活外接程序计划

云操作员可随时将计划添加到先前创建的订阅：

1. 在管理员门户中，选择 "**所有服务**"，然后在 "**管理资源**" 类别下选择 "**用户订阅**"。 选择想要更改的订阅。

2. 选择 "**外接程序**"，然后选择 " **+ 添加**"。  

3. 在 "**添加计划**" 下，选择要作为外接程序的计划。

## <a name="create-a-subscription-as-a-user"></a>以用户身份创建订阅

用户可以登录到用户门户，查找并订阅目录租户（组织）的公共产品/服务和附加计划。

>[!NOTE]
>如果 Azure Stack 中心环境支持[多租户](azure-stack-enable-multitenancy.md)，则还可以从远程目录租户订阅产品/服务。

### <a name="to-subscribe-to-an-offer"></a>订阅产品/服务

1. 登录到 Azure Stack 集线器用户门户，然后选择 "**获取订阅**"。

   ![在 Azure Stack Hub 用户门户中获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. 在 "**获取订阅**" 下的 "**显示名称**" 中，输入订阅的友好名称。 选择 "**产品**/服务"，在 "**选择产品**/服务" 下选择产品/服务。 选择 "**创建**" 以创建订阅。

   ![在 Azure Stack 集线器用户门户中选择产品/服务](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. 订阅产品/服务后，刷新门户以查看哪些服务是新订阅的一部分。

4. 若要查看创建的订阅，请选择 "**所有服务**"，然后在 "**常规**" 类别下选择 "**订阅**"。 选择订阅以查看订阅详细信息。  

### <a name="to-enable-an-add-on-plan-in-your-subscription"></a>在订阅中启用外接程序计划

如果订阅的产品/服务有加载项计划，则可以随时将该计划添加到订阅。  

1. 在用户门户中，选择 "**所有服务**"。 接下来，在 "**常规**" 类别下，选择 "**订阅**"，然后选择要更改的订阅。 如果存在可用的附加计划，则 **+ "添加计划**" 处于活动状态，并显示**附加计划**的磁贴。

   如果 **+ Add 计划**不处于活动状态，则不存在与该订阅关联的产品/服务的附加计划。

1. 选择 " **+ 添加计划**" 或 "**外接程序计划**" 磁贴。 在 "**外接程序计划**" 下，选择要添加的计划。

## <a name="next-steps"></a>后续步骤

了解有关用户现在如何将资源部署到其订阅中的详细信息：

- [多个用户快速入门](../user/azure-stack-quick-windows-portal.md)介绍如何使用 PowerShell、Azure CLI 和用户门户预配 Windows 和 Linux 虚拟机。
- [使用 Azure 资源管理器模板的教程](../user/azure-stack-create-vm-template.md)演示了如何部署运行 Minikube 的 Ubuntu 16.04 虚拟机来管理 Kubernetes 群集。
