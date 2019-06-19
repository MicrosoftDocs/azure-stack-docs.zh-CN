---
title: 若要使用 Azure Stack 容器的 Kubernetes 部署 |Microsoft Docs
description: 了解如何部署 Kubernetes Azure Stack 中使用容器。
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: a33ce68d72ae728c0bfbcb59c83551051176dd8a
ms.sourcegitcommit: c4507a100eadd9073aed0d537d054e394b34f530
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67198700"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>部署 Kubernetes Azure Stack 中使用容器

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。 在预览目前不支持 Azure Stack 离线的场景。

可以按照这篇文章来部署和在单个协调的操作中设置 Kubernetes 资源中的步骤。 步骤使用 Azure 资源管理器解决方案模板。 你将需要有关 Azure Stack 安装，收集所需的信息生成模板，并随后部署到你的云。 Azure Stack 模板不使用全球 Azure 中提供的相同托管的 AKS 服务。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

你可以安装 Kubernetes 中通过 Azure 资源管理器模板在 Azure Stack 上 AKS 引擎生成的。 [Kubernetes](https://kubernetes.io) 是一个开源系统，可以自动部署、缩放和管理容器中的应用程序。 一个[容器](https://www.docker.com/what-container)是图像中。 容器映像是类似于虚拟机 (VM)，但是，与 VM 不同，容器仅包含运行应用程序，例如代码，运行时执行代码、 特定的库和设置所需的资源。

可以使用 Kubernetes 执行以下操作：

- 开发可以大规模伸缩、升级并可快速部署的应用程序。 
- 通过不同的 Helm 应用程序简化应用程序的设计并改进其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松地监视和诊断你的应用程序的运行状况。

你将为所需的支持你的群集节点的计算使用情况付费。 有关详细信息，请参阅 [Azure Stack 中的使用量和计费](../operator/azure-stack-billing-and-chargeback.md)。

## <a name="deploy-kubernetes-to-use-containers"></a>部署用于容器的 Kubernetes

部署 Azure Stack 上的 Kubernetes 群集的步骤将取决于你的标识管理服务。 验证由您安装的 Azure Stack 的标识管理解决方案。 与 Azure Stack 管理员联系以验证你的标识管理服务。

- **Azure Active Directory (Azure AD)**  
有关使用 Azure AD 时安装该群集的说明，请参阅[到 Azure Stack 使用 Azure Active Directory (Azure AD) 中部署 Kubernetes](azure-stack-solution-template-kubernetes-azuread.md)。

- **Active Directory 联合身份验证服务 (AD FS)**  
有关使用 AD FS 时安装该群集的说明，请参阅[到 Azure Stack 使用 Active Directory 联合身份验证服务 (AD FS) 部署 Kubernetes](azure-stack-solution-template-kubernetes-adfs.md)。

## <a name="connect-to-your-cluster"></a>连接到群集

现在，你可以连接到你的群集。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到 master。 在 master 上，可以使用 **kubectl**（Kubernetes 命令行客户端）来管理群集。 有关说明，请参阅 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你可能还会发现，**Helm** 包管理器适用于将应用安装和部署到群集中。 若要了解如何通过群集来安装和使用 Helm，请参阅 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)

[向市场添加 Kubernetes（适用于 Azure Stack 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[将 Kubernetes 部署到 Azure Stack 使用 Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务 (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
