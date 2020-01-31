---
title: 使用 Azure Active Directory （Azure AD）将 Kubernetes 部署到 Azure Stack 集线器
description: 了解如何使用 Azure Active Directory （Azure AD）将 Kubernetes 部署到 Azure Stack 中心。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: e8c8ad8581b9deff792ce2c095bbc3c5f9cee034
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883222"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-azure-active-directory"></a>使用 Azure Active Directory 将 Kubernetes 部署到 Azure Stack 中心

> [!Note]  
> 仅使用 Kubernetes Azure Stack Marketplace 项将群集部署为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用[AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

使用 Azure Active Directory （Azure AD）作为标识管理服务时，你可以按照本文中的步骤为 Kubernetes 部署和设置资源，只需要一个协调的操作即可。

## <a name="prerequisites"></a>必备组件

若要开始，请确保具有适当的权限，并且 Azure Stack 中心已准备就绪。

1. 验证是否可以在 Azure Active Directory （Azure AD）租户中创建应用程序。 对于 Kubernetes 部署，你需要这些权限。

    有关检查权限的说明，请参阅[检查 Azure Active Directory 权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)。

1. 生成 SSH 公钥和私钥对，用于登录到 Azure Stack 集线器上的 Linux VM。 创建群集时需要公钥。

    有关生成密钥的说明，请参阅[SSH 密钥生成](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 请检查 Azure Stack 中心租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址可用于添加新的应用程序。

    无法将群集部署到 Azure Stack 中心**管理员**订阅。 必须使用**用户**订阅。 

1. 如果 marketplace 中没有 Kubernetes 群集，请向 Azure Stack 中心管理员咨询。

## <a name="create-a-service-principal"></a>创建服务主体

在 Azure 中设置服务主体。 服务主体允许应用程序访问 Azure Stack 集线器资源。

1. 登录到全局[Azure 门户](https://portal.azure.com)。

1. 检查是否已使用与 Azure Stack 中心实例关联的 Azure AD 租户登录。 可以通过单击 Azure 工具栏中的 "筛选器" 图标来切换登录。

    ![选择 AD 租户](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. 创建 Azure AD 应用程序。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 通过 [Azure 门户](https://portal.azure.com)登录到 Azure 帐户。  
    b.保留“数据库类型”设置，即设置为“共享”。 选择 " **Azure Active Directory** > **应用注册**" > "**新注册**"。  
    c. 为应用提供名称和 URL。  
    d.单击“下一步”。 选择**支持的帐户类型**。  
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  添加应用程序的 URI `http://localhost`。 选择要创建的应用程序类型的 " **Web** "。 设置值后，选择 "**注册**"。

1. 请记下**应用程序 ID**。 创建群集时，你将需要 ID。 该 ID 被称为 "**服务主体客户端 ID**"。

1. 在服务主体的边栏选项卡中，选择 "**新建客户端密码**"。 **设置** > **键**。 需要为服务主体生成身份验证密钥。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 输入“说明”。

    b.保留“数据库类型”设置，即设置为“共享”。 对于“过期”这一项，请选择“永不过期”。

    c. 选择 **添加** 。 记下密钥字符串。 创建群集时，需要密钥字符串。 密钥被称为**服务主体客户端机密**。

## <a name="give-the-service-principal-access"></a>为服务主体提供访问权限

向服务主体授予对订阅的访问权限，以便主体能够创建资源。

1.  登录到[Azure Stack 中心门户](https://portal.local.azurestack.external/)。

1. 选择 "**所有服务**" > **订阅**。

1. 选择操作员为使用 Kubernetes 群集创建的订阅。

1. 选择 "**访问控制（IAM）** " > 选择 "**添加角色分配**"。

1. 选择 "**参与者**" 角色。

1. 选择为服务主体创建的应用程序名称。 可能需要在搜索框中键入名称。

1. 单击“ **保存**”。

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 打开[Azure Stack 集线器门户](https://portal.local.azurestack.external)。

1. 选择 " **+ 创建资源**" > **计算** > **Kubernetes 群集**"。 单击“创建”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. 基础知识

1. 选择创建 Kubernetes 群集中的**基础知识**。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择**订阅**ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。 这是你为 Azure Stack 集线器安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes 群集设置

1. 选择 "创建 Kubernetes 群集中的**Kubernetes 群集设置**"。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. 输入**LINUX VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的**主配置文件 DNS 前缀**。 此名称必须是区域唯一名称，如 `k8s-12345`。 尝试选择与资源组名称相同的最佳实践。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择**Kubernetes 主池配置文件计数**。 计数包含主池中的节点数。 可能有1到7之间。 此值应为奇数。

1. 选择**Kubernetes 主 vm 的 VMSize**。 这将指定 Kubernetes 主 Vm 的 VM 大小。 

1. 选择**Kubernetes 节点池配置文件计数**。 此计数包含群集中的代理数。 

1. 选择**Kubernetes 节点 vm 的 VMSize**。 这将指定 Kubernetes 节点 Vm 的 VM 大小。 

1. 为 Azure Stack Hub 安装选择**Azure Stack 集线器标识系统** **Azure AD** 。

1. 输入**服务主体 clientId**这由 Kubernetes Azure 云提供程序使用。 Azure Stack 中心管理员创建服务主体时标识为应用程序 ID 的客户端 ID。

1. 输入**服务主体客户端机密**。 这是你在创建服务时设置的客户端密码。

1. 输入**Kubernetes 版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 中心为每个 Azure Stack 中心版本发布自定义 Kubernetes 生成。

### <a name="3-summary"></a>3. 摘要

1. 选择 "摘要"。 边栏选项卡会显示 Kubernetes 群集配置设置的验证消息。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 检查设置。

3. 选择 **"确定"** 以部署群集。

> [!TIP]  
>  如果你对部署有任何疑问，则可以发布问题，或者查看是否有人在[Azure Stack 中心论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中回答了问题。


## <a name="next-steps"></a>后续步骤

[连接到群集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)
