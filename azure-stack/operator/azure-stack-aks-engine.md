---
title: 向 Azure Stack Marketplace 添加 Azure Kubernetes Services （AKS）引擎必备组件 |Microsoft Docs
description: 了解如何将 AKS 引擎先决条件添加到 Azure Stack Marketplace。
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
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 3b6d1b9e91acfdb7a54cf3b7fbc70399bf635c09
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595256"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>向 Azure Stack Marketplace 添加 Azure Kubernetes Services （AKS）引擎必备组件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过将本文中所述的项目添加到 Azure Stack，使用户能够设置 Azure Kubernetes Services （AKS）引擎。 然后，用户可以通过单个协调的操作来部署 Kubernetes 群集。 本文将指导你完成将 AKS 引擎提供给连接和断开连接环境中的用户所需的步骤。 AKS 引擎依赖于服务主体标识，在 marketplace 中是自定义脚本扩展和 AKS 基本映像。

[AKS 引擎](https://github.com/Azure/aks-engine)使用生成的映像 AKS 基本映像。 任何 AKS 引擎版本都依赖于可在 Azure Stack 中使用的特定映像版本。 请查看列出[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)中的 AKS 引擎版本和相应 Kubernetes 版本的表。

> [!IMPORTANT]
> AKS 引擎目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="check-your-users-service-offering"></a>查看用户的服务产品

你的用户将需要一个计划、产品/服务和订阅，以便 Azure Stack 具有足够的空间。 用户通常需要部署多达六个虚拟机的群集，由三个主机和三个工作节点组成。 你需要确保其配额足够大。

如果需要有关规划和设置服务产品的详细信息，请参阅[在 Azure Stack 中提供服务概述](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>创建服务主体和凭据

在 Azure Stack 中，Kubernetes 群集将需要服务主体（SPN）和基于角色的权限。

### <a name="create-an-spn-in-azure-ad"></a>在 Azure AD 中创建 SPN

如果你将 Azure Active Directory （Azure AD）用于标识管理服务，则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端密码创建服务主体。 有关说明，请参阅[创建使用客户端密钥凭据的服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential)。

### <a name="create-an-spn-in-ad-fs"></a>在 AD FS 中创建 SPN

如果为标识管理服务使用 Active Directory 联合服务（AD FS），则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端密码创建服务主体。 有关说明，请参阅[使用客户端密码创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)。

## <a name="add-the-aks-base-image"></a>添加 AKS 基础映像

可以通过从 Azure 获取项，将 AKS 基本映像添加到 marketplace。 但是，如果你的 Azure Stack 已断开连接，请使用以下说明[从 Azure 下载 marketplace 项](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario)以添加该项目。 添加在步骤5中指定的项目。

将以下项目添加到 marketplace：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. `AKS Base Image`。

1. 选择与 AKS 引擎版本匹配的映像版本。 可以在[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)中找到 AKS 基本映像到 AKS 引擎版本的列表。 

    在列表中，选择：
    - **发布者**： Azure Kubernetes 服务
    - **产品/服务**： aks
    - **版本**： AKS 基本映像 16.04-LTS Image 发行版，9月2019（2019.09.19 或 Version 映射到 AKS engine）

1. 选择“下载”。

## <a name="add-a-custom-script-extension"></a>添加自定义脚本扩展

可以通过从 Azure 获取项，将自定义脚本添加到 marketplace。 但是，如果 Azure Stack 已断开连接，请使用[从 Azure 下载 marketplace 项中](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario)的说明添加项。  添加在步骤5中指定的项目。

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. `Custom Script for Linux`。

1. 选择具有以下配置文件的服务器：
   - **套餐**：适用于 Linux 2.0 的自定义脚本
   - **版本**：2.0.6 （或最新版本）
   - **发布者**：Microsoft Corp

     > [!Note]  
     > 可能列出了适用于 Linux 的自定义脚本的多个版本。 你将需要添加项的最后一个版本。

1. 选择“下载”。

## <a name="next-steps"></a>后续步骤

[Azure Stack 上的 AKS 引擎是什么？](../user/azure-stack-kubernetes-aks-engine-overview.md)

[在 Azure Stack 中提供服务概述](service-plan-offer-subscription-overview.md)
