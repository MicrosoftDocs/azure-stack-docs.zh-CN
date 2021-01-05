---
title: 部署 Kubernetes 以使用 Azure Stack Hub 容器
description: 了解如何部署 Kubernetes，以便将容器与 Azure Stack Hub 配合使用。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 916a053f656d1509df66f1206bb4073fe83ceee2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873854"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack-hub"></a>部署 Kubernetes，将容器与 Azure Stack Hub 配合使用

> [!NOTE]  
> 仅使用 Kubernetes Azure Stack 市场项将部署群集作为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用 [AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

可以按照本文中的步骤，通过单个协调的操作来部署和设置 Kubernetes 的资源。 该步骤使用 Azure 资源管理器解决方案模板。 需收集有关 Azure Stack Hub 安装的必需信息，生成模板，然后再部署到云。 Azure Stack 中心模板不使用在全球 Azure 中提供的相同的托管 AKS 服务。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

可以使用由 Azure Stack Hub 上的 AKS 引擎生成的 Azure 资源管理器模板来安装 Kubernetes。 [Kubernetes](https://kubernetes.io) 是一个开源系统，可以自动部署、缩放和管理容器中的应用程序。 [容器](https://www.docker.com/what-container)位于映像中。 容器映像类似于虚拟机 (VM)，但与 VM 不同的是，容器只包含运行应用程序所需的资源，例如代码、执行代码所需的运行时、特定库以及设置。

可以使用 Kubernetes 执行以下操作：

- 开发可以大规模伸缩、升级并可快速部署的应用程序。 
- 通过不同的 Helm 应用程序简化应用程序的设计并改进其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松监视和诊断应用程序的运行状况。

将仅对为你的群集提供支持的节点所需的计算使用量收费。 有关详细信息，请参阅 [Azure Stack Hub 中的使用情况和计费](../operator/azure-stack-billing-and-chargeback.md)。

## <a name="deploy-kubernetes-to-use-containers"></a>部署 Kubernetes 以使用容器

在 Azure Stack Hub 上部署 Kubernetes 群集的步骤将取决于标识管理服务。 验证安装 Azure Stack Hub 时所使用的标识管理解决方案。 请与 Azure Stack Hub 管理员联系，以验证标识管理服务。

- **Azure Active Directory (Azure AD)**  
有关使用 Azure AD 时安装群集的说明，请参阅[使用 Azure Active Directory (Azure AD) 将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-azuread.md)。

- **Active Directory 联合身份验证服务 (AD FS)**  
有关使用 AD FS 时安装群集的说明，请参阅[使用 Active Directory 联合身份验证服务 (AD FS) 将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-adfs.md)。

## <a name="connect-to-your-cluster"></a>连接到群集

现在可以连接到群集了。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到 master。 在 master 上，可以使用 **kubectl**（Kubernetes 命令行客户端）来管理群集。 有关说明，请参阅 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你可能还会发现，**Helm** 包管理器适用于将应用安装和部署到群集中。 若要了解如何通过群集来安装和使用 Helm，请参阅 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)

[向市场添加 Kubernetes（适用于 Azure Stack Hub 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[使用 Azure Active Directory (Azure AD) 将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-azuread.md)

[使用 Active Directory 联合身份验证服务 (AD FS) 将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-adfs.md)

[Azure 上的 Kubernetes](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
