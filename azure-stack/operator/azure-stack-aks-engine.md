---
title: 将 Azure Kubernetes Services (AKS) 引擎必备组件添加到 Azure Stack 中心市场
description: 了解如何将 AKS 引擎必备组件添加到 Azure Stack Hub 市场。
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 810aa253eb4872a58ea2e52070bb42eaaa1f6aaa
ms.sourcegitcommit: 719569bb9e3f9924494a9229b4f2d211ae3f4f74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2020
ms.locfileid: "90717982"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-hub-marketplace"></a>将 Azure Kubernetes 服务 (AKS) 引擎必备组件添加到 Azure Stack Hub 市场

可以为用户设置 Azure Kubernetes 服务 (AKS) 引擎。 将本文中所述的项添加到 Azure Stack Hub。 之后，用户通过一次协调的操作即可部署 Kubernetes 群集。 本文指导你完成让用户在联网和离线环境中都能使用 AKS 引擎所需的步骤。 AKS 引擎依赖于服务主体标识，以及市场中的自定义脚本扩展和 AKS 基础映像。 AKS 引擎要求运行 [Azure Stack Hub 1910](release-notes.md?view=azs-1910&preserve-view=true) 或更高版本。

> [!NOTE]  
> 可以在 [AKS 引擎发行说明](/azure-stack/user/kubernetes-aks-engine-release-notes#aks-engine-and-azure-stack-version-mapping)中找到 Azure Stack 集线器到 AKS 引擎版本号的映射。

## <a name="check-your-users-service-offering"></a>检查用户的服务产品

你的用户将需要具备带有足够空间的 Azure Stack Hub 计划、套餐和订阅。 通常，用户需要部署最多由六个虚拟机（包含三个主机和三个工作器节点）组成的群集。 你需要确保其配额空间足够大。

如果需要有关计划和设置服务产品的详细信息，请参阅[有关在 Azure Stack Hub 中提供服务的概述](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>创建服务主体和凭据

Kubernetes 群集将需要 Azure Stack Hub 中的服务主体 (SPN) 和基于角色的权限。

### <a name="create-an-spn-in-azure-ad"></a>在 Azure AD 中创建 SPN

如果将 Azure Active Directory (Azure AD) 用于标识管理服务，则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端密码创建服务主体。 有关说明，请参阅[创建使用客户端密码凭据的服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential)。

### <a name="create-an-spn-in-ad-fs"></a>在 AD FS 中创建 SPN

如果将 Active Directory 联合身份验证服务 (AD FS) 用于标识管理服务，则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端密码创建服务主体。 有关说明，请参阅[使用客户端密码创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)。

## <a name="add-the-aks-base-image"></a>添加 AKS 基础映像

可通过从 Azure 获取 AKS 基础映像将该项添加到市场。 但是，如果 Azure Stack Hub 处于离线状态，请按照[从 Azure 下载市场项](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)中的说明来添加该项。 添加第 5 步中指定的项。

将以下项添加到市场：

1. 登录到管理门户 `https://adminportal.local.azurestack.external`。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `AKS Base`。

1. 选择与 AKS 引擎版本匹配的映像版本。 可在[受支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)中找到与 AKS 引擎版本对应的 AKS 基础映像的列表。 

    在该列表中，选择：
    - **发布者**：Azure Kubernetes 服务
    - **套餐**：aks
    - **名称**： AKS Base Ubuntu 16.04-LTS Image 发行版，8月 2020 (2020.09.14 或映射到 AKS engine) 
    - **版本**：2020.09.14

1. 选择“下载”。

## <a name="add-a-custom-script-extension"></a>添加自定义脚本扩展

可通过从 Azure 获取自定义脚本将该项添加到市场。 但是，如果 Azure Stack Hub 处于离线状态，请按照[从 Azure 下载市场项](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)中的说明来添加该项。  添加第 5 步中指定的项。

1. 打开管理门户 `https://adminportal.local.azurestack.external`。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Custom Script for Linux`。

1. 选择具有以下配置文件的服务器：
   - **产品/服务**：适用于 Linux 的自定义脚本 2.0
   - **版本**：2.0.6（或最新版本）
   - **发布者**：Microsoft Corp

     > [!Note]  
     > 可能会列出适用于 Linux 的自定义脚本的多个版本。 你需要添加该项的最新版本。

1. 选择“下载”。

## <a name="next-steps"></a>后续步骤

[ Azure Stack Hub 上的 AKS 引擎是什么？](../user/azure-stack-kubernetes-aks-engine-overview.md)

[在 Azure Stack Hub 中提供服务概述](service-plan-offer-subscription-overview.md)
