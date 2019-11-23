---
title: Add the Azure Kubernetes Services (AKS) engine prerequisites to the Azure Stack Marketplace | Microsoft Docs
description: Learn how to add AKS engine prerequisites to the Azure Stack Marketplace.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: ee19c6ee32960c52bcf7a4918c3d1e48406129c3
ms.sourcegitcommit: 31e04af4d405215ef200aba0b40d601fc5ca7662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74391548"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Add the Azure Kubernetes Services (AKS) engine prerequisites to the Azure Stack Marketplace

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

You can enable your users to set up the Azure Kubernetes Services (AKS) Engine by adding the items described in this article to your Azure Stack. Your users can then deploy a Kubernetes cluster in a single, coordinated operation. This article walks you through the steps you need to make the AKS engine available to your users in both connected and disconnected environments. The AKS engine depends on a service principle identity, and in the marketplace, a Custom Script extension and the AKS Base Image.

## <a name="check-your-users-service-offering"></a>Check your user's service offering

Your users will need a plan, offer, and subscription to Azure Stack with enough space. Users will often want to deploy clusters of up to six virtual machines, made of three masters and three worker nodes. You will want to make sure they have a large enough quota.

If you need more information about planning and setting up a service offering, see [Overview of offering services in Azure Stack](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>Create a service principal and credentials

The Kubernetes cluster will need service principal (SPN) and role-based permissions in Azure Stack.

### <a name="create-an-spn-in-azure-ad"></a>Create an SPN in Azure AD

If you use Azure Active Directory (Azure AD) for your identity management service, you will need to create a service principal for users deploying a Kubernetes cluster. Create a service principal using a client secret. For instructions, see [Create a service principal that uses a client secret credential](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Create an SPN in AD FS

If you use Active Directory Federated Services (AD FS) for your identity management service, you will need to create a service principal for users deploying a Kubernetes cluster. Create a service principal using a client secret. For instructions, see [Create a service principal using a client secret](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Add the AKS Base Image

You can add the AKS Base Image to the marketplace by getting the item from Azure. However, if your Azure Stack is disconnected, use these instructions [Download marketplace items from Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) to add the item. Add the item specified in step 5.

Add the following item to the marketplace:

1. Sign in to the [Administration portal](https://adminportal.local.azurestack.external).

1. Select **All services**, and then under the **ADMINISTRATION** category, select **Marketplace management**.

1. Select **+ Add from Azure**.

1. 输入 `AKS Base` 。

1. Select the image version that matches the version of the AKS engine. You can find listing of AKS Base Image to AKS engine version at [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). 

    In the list, select:
    - **Publisher**: Azure Kubernetes Service
    - **Offer**: aks
    - **Version**: AKS Base Image 16.04-LTS Image Distro, October 2019 (2019.10.24 or version that maps to AKS engine)

1. Select **Download.**

## <a name="add-a-custom-script-extension"></a>Add a Custom Script extension

You can add the custom script to the marketplace by getting the item from Azure. However, if your Azure Stack is disconnected, use the instructions [Download marketplace items from Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) to add the item.  Add the item specified in step 5.

1. Open the [Administration portal](https://adminportal.local.azurestack.external).

1. Select **ALL services** and then under the **ADMINISTRATION** category, select **Marketplace Management**.

1. Select **+ Add from Azure**.

1. 输入 `Custom Script for Linux` 。

1. Select the script with the following profile:
   - **Offer**: Custom Script for Linux 2.0
   - **Version**: 2.0.6 (or latest version)
   - **Publisher**: Microsoft Corp

     > [!Note]  
     > More than one version of the Custom Script for Linux may be listed. You will need to add the last version of the item.

1. Select **Download.**

## <a name="next-steps"></a>后续步骤

[What is the AKS engine on Azure Stack?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Overview of offering services in Azure Stack](service-plan-offer-subscription-overview.md)
