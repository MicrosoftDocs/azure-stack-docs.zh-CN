---
title: 使用 Active Directory 联合服务（AD FS）将 Kubernetes 部署到 Azure Stack 中心 |Microsoft Docs
description: 了解如何使用 Active Directory 联合服务（AD FS）将 Kubernetes 部署到 Azure Stack 中心。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: nav
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: b02f4f0deddd7a7eae493d4af02315f636e594c9
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535954"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>使用 Active Directory 联合服务将 Kubernetes 部署到 Azure Stack 中心

> [!Note]  
> 仅使用 Kubernetes Azure Stack Marketplace 项将群集部署为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用 [AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

可以按照本文中的步骤部署和设置 Kubernetes 的资源。 Active Directory 联合服务（AD FS）是标识管理服务时，请使用以下步骤。

## <a name="prerequisites"></a>必备组件 

若要开始，请确保具有适当的权限，并且 Azure Stack 中心已准备就绪。

1. 生成 SSH 公钥和私钥对，用于登录到 Azure Stack 集线器上的 Linux VM。 创建群集时需要公钥。

    有关生成密钥的说明，请参阅[SSH 密钥生成](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 请检查 Azure Stack 中心租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址可用于添加新的应用程序。

    无法将群集部署到 Azure Stack 中心**管理员**订阅。 必须使用**用户**订阅。 

1. 如果 marketplace 中没有 Kubernetes 群集，请向 Azure Stack 中心管理员咨询。

## <a name="create-a-service-principal"></a>创建服务主体

使用 AD FS 作为标识解决方案时，你需要与 Azure Stack 中心管理员合作来设置你的服务主体。 服务主体允许应用程序访问 Azure Stack 集线器资源。

1. Azure Stack 中心管理员提供有关服务主体的信息。 服务主体信息应该如下所示：

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. 为你的订阅分配作为参与者的新服务主体角色。 有关说明，请参阅[分配角色](../operator/azure-stack-add-users-adfs.md)。

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 打开[Azure Stack 集线器门户](https://portal.local.azurestack.external)。

1. 选择 " **+ 创建资源**" > **计算** > **Kubernetes 群集**"。 选择“创建”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. 基础知识

1. 选择创建 Kubernetes 群集中的**基础知识**。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择**订阅**ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。 这是你为 Azure Stack 集线器安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes 群集设置

1. 选择 "创建 Kubernetes 群集中的**Kubernetes 群集设置**"。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. 输入**LINUX VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的**主配置文件 DNS 前缀**。 此名称必须是区域唯一名称，如 `k8s-12345`。 尝试选择与资源组名称相同的最佳实践。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择**Kubernetes 主池配置文件计数**。 计数包含主池中的节点数。 可能有1到7之间。 此值应为奇数。

1. 选择**Kubernetes 主 vm 的 VMSize**。

1. 选择**Kubernetes 节点池配置文件计数**。 此计数包含群集中的代理数。 

1. 选择**Kubernetes 节点 vm 的 VMSize**。 这将指定 Kubernetes 节点 Vm 的 VM 大小。 

1. 为 Azure Stack 中心安装选择**Azure Stack 集线器标识系统**的**ADFS** 。

1. 输入**服务主体 clientId**这由 Kubernetes Azure 云提供程序使用。 Azure Stack 中心管理员创建服务主体时标识为应用程序 ID 的客户端 ID。

1. 输入**服务主体客户端机密**。 这是你从 Azure Stack 中心管理员为你的 AD FS 服务主体提供的客户端密码。

1. 输入**Kubernetes 版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 中心为每个 Azure Stack 中心版本发布自定义 Kubernetes 生成。

### <a name="3-summary"></a>3. 摘要

1. 选择 "摘要"。 边栏选项卡会显示 Kubernetes 群集配置设置的验证消息。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 查看设置。

3. 选择 **"确定"** 以部署群集。

> [!TIP]  
>  如果你对部署有任何疑问，则可以发布问题，或者查看是否有人在[Azure Stack 中心论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中回答了问题。 

## <a name="next-steps"></a>后续步骤

[连接到群集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)