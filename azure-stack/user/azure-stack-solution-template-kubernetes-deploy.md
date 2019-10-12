---
title: 部署 Kubernetes 以使用 Azure Stack 容器 |Microsoft Docs
description: 了解如何部署 Kubernetes，以便 Azure Stack 使用容器。
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 74cad0b1f41c5c764bef361f3f521162eec59198
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277655"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>部署 Kubernetes 以使用具有 Azure Stack 的容器

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。 预览版目前不支持 Azure Stack 脱接方案。 仅在开发和测试方案中使用 marketplace 项。

可以按照本文中的步骤，通过单个协调的操作来部署和设置 Kubernetes 的资源。 步骤使用 Azure 资源管理器解决方案模板。 需要收集 Azure Stack 安装所需的信息，生成模板，然后将其部署到云。 Azure Stack 模板不使用在全球 Azure 中提供的相同的托管 AKS 服务。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

你可以使用 Azure Stack 上的 AKS 引擎生成的 Azure 资源管理器模板来安装 Kubernetes。 [Kubernetes](https://kubernetes.io) 是一个开源系统，可以自动部署、缩放和管理容器中的应用程序。 [容器](https://www.docker.com/what-container)在映像中。 容器映像类似于虚拟机（VM），但与 VM 不同，容器只包含运行应用程序所需的资源，例如代码、运行时、特定的库和设置。

可以使用 Kubernetes 执行以下操作：

- 开发可以大规模伸缩、升级并可快速部署的应用程序。 
- 通过不同的 Helm 应用程序简化应用程序的设计并改进其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松监视和诊断应用程序的运行状况。

只需对支持群集的节点所需的计算使用情况收费。 有关详细信息，请参阅 [Azure Stack 中的使用量和计费](../operator/azure-stack-billing-and-chargeback.md)。

## <a name="deploy-kubernetes-to-use-containers"></a>部署 Kubernetes 以使用容器

Azure Stack 上部署 Kubernetes 群集的步骤将取决于你的标识管理服务。 验证 Azure Stack 安装所使用的标识管理解决方案。 请与 Azure Stack 管理员联系以验证身份管理服务。

- **Azure Active Directory (Azure AD)**  
有关使用 Azure AD 时安装群集的说明，请参阅[使用 Azure Active Directory （Azure AD）将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-azuread.md)。

- **Active Directory 联合身份验证服务（AD FS）**  
有关使用 AD FS 时安装群集的说明，请参阅[使用 Active Directory 联合服务（AD FS）将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-adfs.md)。

## <a name="connect-to-your-cluster"></a>连接到群集

你现在已准备好连接到群集。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到 master。 在 master 上，可以使用 **kubectl**（Kubernetes 命令行客户端）来管理群集。 有关说明，请参阅 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你可能还会发现，**Helm** 包管理器适用于将应用安装和部署到群集中。 若要了解如何通过群集来安装和使用 Helm，请参阅 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)

[向市场添加 Kubernetes（适用于 Azure Stack 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[使用 Azure Active Directory （Azure AD）将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-azuread.md)

[使用 Active Directory 联合服务（AD FS）将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-adfs.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
