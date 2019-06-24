---
title: 将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务 (AD FS) |Microsoft Docs
description: 了解如何将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务 (AD FS)。
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 1a2dbe009a6953990ce59e930490cc48cc0dd458
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308738"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。 在预览目前不支持 Azure Stack 离线的场景。 仅使用 marketplace 项进行开发和测试方案。

可以按照这篇文章来部署和设置 Kubernetes 资源中的步骤。 Active Directory 联合身份验证服务 (AD FS) 是标识管理服务，请执行以下步骤。

## <a name="prerequisites"></a>必备组件 

开始之前，请确保你有适当的权限且 Azure Stack 已就绪。

1. 生成一个 SSH 公钥和私钥对，用于登录到 Azure Stack 上的 Linux VM。 创建群集时需要的公钥。

    有关如何生成密钥的说明，请参阅 [SSH 密钥生成](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 检查你在 Azure Stack 租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址来添加新的应用程序。

    此群集不能部署到 Azure Stack **管理员**订阅。 必须使用**用户**订阅。 

1. 如果您没有在 marketplace 中的 Kubernetes 群集，请与 Azure Stack 管理员联系。

## <a name="create-a-service-principal"></a>创建服务主体

您需要使用 Azure Stack 管理员可以使用 AD FS 作为标识解决方案时设置服务主体。 服务主体允许应用程序访问的 Azure Stack 资源。

1. Azure Stack 管理员为你提供的服务主体的信息。 服务主体信息应如下所示：

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. 将新的服务主体作为参与者角色分配给你的订阅。 有关说明，请参阅[将角色分配](../operator/azure-stack-add-users-adfs.md)。

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

1. 选择“+ 创建资源”   > “计算”   > “Kubernetes 群集”  。 选择“创建”  。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基础

1. 在“创建 Kubernetes 群集”中选择“基本信息”。 

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择**订阅** ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。  这是为 Azure Stack 安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 群集设置

1. 在“创建 Kubernetes 群集”中选择“Kubernetes 群集设置”。 

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. 输入**Linux VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的**主配置文件 DNS 前缀**。 这必须是特定于区域的名称，例如 `k8s-12345`。 最佳做法是尝试选择与资源组名称相同的名称。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择**Kubernetes 主机池配置文件计数**。 此计数包含主池中的节点数。 其范围为 1 到 7。 此值应当为奇数。

1. 选择“Kubernetes 主 VM 的 VMSize”。 

1. 选择**Kubernetes 节点池配置文件计数**。 此计数包含群集中的代理数。 

1. 选择**Kubernetes 节点 Vm 的 VMSize**。 这指定 Kubernetes 节点 VM 的 VM 大小。 

1. 选择**ADFS**有关**Azure Stack 的标识系统**为 Azure Stack 安装。

1. 输入**服务主体 clientId**由 Kubernetes Azure 云提供程序。 Azure Stack 管理员创建服务主体时标识应用程序 ID 为客户端 ID。

1. 输入**服务主体客户端机密**。 这是从 Azure Stack 管理员为你的 AD FS 服务主体提供给你的客户端机密。

1. 输入**Kubernetes 版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

### <a name="3-summary"></a>3.摘要

1. 选择“摘要”。 此边栏选项卡显示针对 Kubernetes 群集配置设置的验证消息。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 复查你的设置。

3. 选择“确定”  以部署群集。

> [!TIP]  
>  如果对你的部署有疑问，可以在 [Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中发布问题或查看是否已经有人已回答了该问题。 

## <a name="next-steps"></a>后续步骤

[连接到群集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)