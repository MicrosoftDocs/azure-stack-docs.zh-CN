---
title: 使用 Active Directory 联合身份验证服务 (AD FS) 将 Kubernetes 部署到 Azure Stack Hub
description: 了解如何使用 Active Directory 联合身份验证服务 (AD FS) 将 Kubernetes 部署到 Azure Stack Hub。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 268157d491b1605ecce442cc4cb348e76657fec2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873939"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>使用 Active Directory 联合身份验证服务将 Kubernetes 部署到 Azure Stack Hub

> [!NOTE]  
> 仅使用 Kubernetes Azure Stack 市场项将部署群集作为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用 [AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

可以按照本文中的步骤部署和设置 Kubernetes 的资源。 如果 Active Directory 联合身份验证服务 (AD FS) 是你的标识管理服务，请按照这些步骤进行操作。

## <a name="prerequisites"></a>先决条件 

开始之前，请确保你有适当的权限且 Azure Stack Hub 已就绪。

1. 生成一个 SSH 公钥和私钥对，用于登录到 Azure Stack Hub 上的 Linux VM。 在创建群集时需要此公钥。

    有关如何生成密钥的说明，请参阅 [SSH 密钥生成](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 检查你在 Azure Stack Hub 租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址来添加新的应用程序。

    此群集不能部署到 Azure Stack Hub“管理员”订阅。 必须使用 **用户** 订阅。 

1. 如果你的市场中没有 Kubernetes 群集，请联系 Azure Stack Hub 管理员。

## <a name="create-a-service-principal"></a>创建服务主体

使用 AD FS 作为标识解决方案时，需要与 Azure Stack Hub 管理员配合设置服务主体。 借助服务主体，应用程序可以访问 Azure Stack Hub 资源。

1. Azure Stack Hub 管理员会提供有关服务主体的信息。 服务主体信息应如下所示：

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. 为新服务主体分配一个角色，作为订阅的参与者。 有关说明，请参阅[分配角色](../operator/azure-stack-add-users-adfs.md)。

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 打开 Azure Stack Hub 门户 `https://portal.local.azurestack.external`。

1. 选择“+ 创建资源” > “计算” > “Kubernetes 群集”。 选择“创建” 。

    ![此屏幕截图显示了用于创建 Kubernetes 群集的页面。](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基础知识

1. 在“创建 Kubernetes 群集”中选择“基本信息”。

    ![此屏幕截图显示了添加 Kubernetes 群集基本信息的位置。](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择 **订阅** ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。 这是为 Azure Stack Hub 安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 群集设置

1. 在“创建 Kubernetes 群集”中选择“Kubernetes 群集设置”。

    ![此屏幕截图显示了配置 Kubernetes 群集设置的步骤。](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. 输入 Linux VM 管理员用户名。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的 **主配置文件 DNS 前缀**。 这必须是特定于区域的名称，例如 `k8s-12345`。 最佳做法是尝试选择与资源组名称相同的名称。

    > [!NOTE]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择“Kubernetes 主池配置文件计数”。 此计数包含主池中的节点数。 其范围为 1 到 7。 此值应当为奇数。

1. 选择“Kubernetes 主 VM 的 VMSize”。

1. 选择“Kubernetes 节点池配置文件计数”。 此计数包含群集中的代理数。 

1. 选择“Kubernetes 节点 VM 的 VMSize”。 这指定 Kubernetes 节点 VM 的 VM 大小。 

1. 对于 Azure Stack Hub 安装，选择“Azure Stack Hub 标识系统”的 ADFS 。

1. 输入“服务主体 ClientId”，供 Kubernetes Azure 云提供程序使用。 Azure Stack Hub 管理员创建服务主体时标识为应用程序 ID 的客户端 ID。

1. 输入“服务主体客户端机密”。 这是 Azure Stack Hub 管理员提供的 AD FS 服务主体的客户端机密。

1. 输入“Kubernetes 版本”。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack Hub 为每个 Azure Stack Hub 版本发布一个自定义 Kubernetes 内部版本。

### <a name="3-summary"></a>3.摘要

1. 选择“摘要”。 此边栏选项卡显示针对 Kubernetes 群集配置设置的验证消息。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 复查你的设置。

3. 选择“确定”以部署群集。

> [!TIP]  
>  如果对你的部署有疑问，可以在 [Azure Stack Hub 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)发布问题或查看是否已经有人回答了该问题。 

## <a name="next-steps"></a>后续步骤

[连接到群集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)