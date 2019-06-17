---
title: 将 Kubernetes 部署到 Azure Stack 使用 Azure Active Directory (Azure AD) |Microsoft Docs
description: 了解如何将 Kubernetes 部署到 Azure Stack 使用 Azure Active Directory (Azure AD)。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 05/17/2019
ms.openlocfilehash: a4fe557175aaa4e2faa6c120645c409c0b3449f5
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138930"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>将 Kubernetes 部署到 Azure Stack 使用 Azure Active Directory

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。 Azure Stack 离线的场景中当前不支持预览。

作为标识管理服务，请按照在本文中部署和 Kubernetes 时使用 Azure Active Directory (Azure AD) 的资源设置步骤。 

## <a name="prerequisites"></a>必备组件

开始之前，请确保你有适当的权限且 Azure Stack 已就绪。

1. 验证是否可以在 Azure Active Directory (Azure AD) 租户中创建应用程序。 需要这些权限才能进行 Kubernetes 部署。

    有关如何检查权限的说明，请参阅[检查 Azure Active Directory 权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)。

1. 生成 SSH 公钥和私钥对中登录到 Linux 虚拟机 (VM)） 在 Azure Stack 上。 创建群集时，将需要的公钥。

    有关如何生成密钥的说明，请参阅 [SSH 密钥生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

1. 检查 Azure Stack 租户门户中具有有效的订阅，并且有足够的公共 IP 地址可用于添加新的应用程序。

    无法将群集部署到 Azure Stack**管理员**订阅。 必须使用**用户**订阅。 

1. 如果在 marketplace 中没有 Kubernetes 群集，与管理员联系 Azure Stack。

## <a name="create-a-service-principal"></a>创建服务主体

服务主体允许应用程序访问的 Azure Stack 资源。 

1. 登录到全球 [Azure 门户](https://portal.azure.com)。

1. 检查是否已使用与 Azure Stack 实例关联的 Azure AD 租户登录。 可以通过单击 Azure 工具栏上的筛选器图标切换你的登录。

    ![选择你的 AD 租户](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. 创建 Azure AD 应用程序。

    a. 通过 [Azure 门户](https://portal.azure.com)登录到 Azure 帐户。  
    b. 选择“Azure Active Directory” > “应用注册” > “新建注册”。     
    c. 提供应用名称和 URL。  
    d. 选择“支持的帐户类型”。   
    e.  添加`http://localhost`为应用程序的 URI。 选择**Web**为你想要创建的应用程序的类型。 设置这些值后，选择“注册”  。

1. 请记下**应用程序 ID**。 创建群集时，将需要的 ID。 此 ID 称为“服务主体客户端 ID”。 

1. 服务主体的边栏选项卡，选择**新的客户端机密** > **设置** > **密钥**，然后生成身份验证密钥服务主体中。

    a. 输入“说明”  。

    b. 对于“过期”这一项，请选择“永不过期”。  

    c. 选择 **添加** 。 记下密钥字符串。 创建群集时，你将需要的键字符串。 此密钥称为“服务主体客户端机密”。 

## <a name="give-the-service-principal-access"></a>为服务主体提供访问权限

为服务主体提供对订阅的访问权限，使该主体能够创建资源。

1.  登录到 [Azure Stack 门户](https://portal.local.azurestack.external/)。

1. 选择“所有服务” > “订阅”。  

1. 选择你的操作员创建的用于使用 Kubernetes 群集的订阅。

1. 选择**访问控制 (IAM)** > 选择**添加的角色分配**。

1. 选择“参与者”角色。 

1. 选择创建为你的服务主体的应用程序名称。 可能需要在搜索框中键入名称。

1. 单击“ **保存**”。

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

1. 选择“+ 创建资源”   > “计算”   > “Kubernetes 群集”  。 然后单击“创建”  。

    ![在 Azure Stack 中创建 Kubernetes 群集](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基础

1. 在“创建 Kubernetes 群集”中选择“基本信息”。 

    ![配置基本设置的 Kubernetes 群集](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择**订阅** ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。  此位置是为 Azure Stack 安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 群集设置

1. 在“创建 Kubernetes 群集”中选择“Kubernetes 群集设置”。 

    ![Kubernetes 群集设置](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. 输入 **Linux VM 管理员用户名**。 此名称是 Kubernetes 群集的一部分的 Linux Vm 和 DVM 的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入**母版配置文件 DNS 前缀**。 此名称必须是区域的唯一的如`k8s-12345`。 请尝试使其符合作为最佳实践的资源组名称。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择“Kubernetes 主池配置文件计数”  。 此计数包含主池中的节点数。 其范围为 1 到 7。 此值应当为奇数。

1. 选择“Kubernetes 主 VM 的 VMSize”。 

1. 选择“Kubernetes 节点池配置文件计数”。  此计数包含群集中的代理数。 

1. 选择“存储配置文件”。  可以选择“Blob 磁盘”或“托管磁盘”。   此配置文件指定 VM 大小的 Kubernetes 节点 Vm。 

1. 选择**Azure AD**有关**Azure Stack 的标识系统**为 Azure Stack 安装。 

1. 输入**服务主体 ClientId**。 Kubernetes Azure 云提供程序使用此标识符。 客户端 ID 被称为应用程序 ID 时创建服务主体。

1. 输入在创建服务主体时创建的**服务主体客户端机密**。

1. 输入 **Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序的版本号。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

### <a name="3-summary"></a>3.摘要

1. 选择“摘要”。 此边栏选项卡显示针对 Kubernetes 群集配置设置的验证消息。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 复查你的设置。

3. 选择“确定”  以部署群集。

> [!TIP]  
>  如果你的部署有疑问，发布你的问题或请参阅如果有人已回答了问题中的[Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。


## <a name="next-steps"></a>后续步骤

[连接到群集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)
