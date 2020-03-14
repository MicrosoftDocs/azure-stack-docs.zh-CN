---
title: 部署 Kubernetes 以使用 Azure Stack 中心容器
description: 了解如何部署 Kubernetes，以便将容器与 Azure Stack 中心一起使用。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 5fa9c506b2e030adbf521191a623579f56f1ae0f
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294249"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack-hub"></a>部署 Kubernetes，将容器与 Azure Stack 中心配合使用

> [!Note]  
> 仅使用 Kubernetes Azure Stack Marketplace 项将群集部署为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用[AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

可以按照本文中的步骤，通过单个协调的操作来部署和设置 Kubernetes 的资源。 步骤使用 Azure 资源管理器解决方案模板。 需要收集 Azure Stack 集线器安装所需的信息，生成模板，然后将其部署到云。 Azure Stack 中心模板不使用在全球 Azure 中提供的相同的托管 AKS 服务。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

你可以使用 Azure Stack 中心上的 AKS 引擎生成的 Azure 资源管理器模板来安装 Kubernetes。 [Kubernetes](https://kubernetes.io)是一个开源系统，用于自动部署、缩放和管理容器中的应用程序。 [容器](https://www.docker.com/what-container)在映像中。 容器映像类似于虚拟机（VM），但与 VM 不同，容器只包含运行应用程序所需的资源，例如代码、运行时、特定的库和设置。

你可以使用 Kubernetes 来执行以下操作：

- 开发可以大规模伸缩、升级并可快速部署的应用程序。 
- 简化应用程序的设计，并通过不同的 Helm 应用程序提高其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松监视和诊断应用程序的运行状况。

只需对支持群集的节点所需的计算使用情况收费。 有关详细信息，请参阅[Azure Stack 中心中的使用情况和计费](../operator/azure-stack-billing-and-chargeback.md)。

## <a name="deploy-kubernetes-to-use-containers"></a>部署 Kubernetes 以使用容器

在 Azure Stack 集线器上部署 Kubernetes 群集的步骤将取决于你的标识管理服务。 验证 Azure Stack 集线器的安装所使用的标识管理解决方案。 请与 Azure Stack 中心管理员联系，以验证身份管理服务。

- **Azure Active Directory (Azure AD)**  
有关使用 Azure AD 时安装群集的说明，请参阅[使用 Azure Active Directory （Azure AD）将 Kubernetes 部署到 Azure Stack 中心](azure-stack-solution-template-kubernetes-azuread.md)。

- **Active Directory 联合身份验证服务（AD FS）**  
有关使用 AD FS 时安装群集的说明，请参阅[使用 Active Directory 联合服务（AD FS）将 Kubernetes 部署到 Azure Stack 中心](azure-stack-solution-template-kubernetes-adfs.md)。

## <a name="connect-to-your-cluster"></a>连接到群集

你现在已准备好连接到群集。 可在群集资源组中找到 master，并将其命名为 `k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到主节点。 在主节点上，可以使用**kubectl**（Kubernetes 命令行客户端）来管理群集。 有关说明，请参阅[Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你还可以找到**Helm**程序包管理器，用于将应用程序安装和部署到群集。 有关在群集上安装和使用 Helm 的说明，请参阅[helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)

[将 Kubernetes 添加到 Marketplace （适用于 Azure Stack 中心操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[使用 Azure Active Directory （Azure AD）将 Kubernetes 部署到 Azure Stack 集线器](azure-stack-solution-template-kubernetes-azuread.md)

[使用 Active Directory 联合服务（AD FS）将 Kubernetes 部署到 Azure Stack 中心](azure-stack-solution-template-kubernetes-adfs.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
