---
title: 将 Kubernetes 添加到 Azure Stack Hub 市场
titleSuffix: Azure Stack Hub
description: 了解如何将 Kubernetes 添加到 Azure Stack Hub 市场。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 03/24/2020v
ms.openlocfilehash: b96b8a2bed340201b9450b37b1aad166ebbdf26d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869230"
---
# <a name="add-kubernetes-to-azure-stack-hub-marketplace"></a>将 Kubernetes 添加到 Azure Stack Hub 市场

> [!note]  
> 仅使用 Kubernetes Azure Stack Hub 市场项部署群集作为概念证明。 有关 Azure Stack Hub 上支持的 Kubernetes 群集，请使用 [AKS 引擎](azure-stack-aks-engine.md)。

可以将 Kubernetes 作为市场项提供给用户。 然后，用户可以通过单个协调的操作部署 Kubernetes。

本文着眼于使用 Azure 资源管理器模板为独立的 Kubernetes 群集部署和预配资源。 在开始之前，请检查 Azure Stack Hub 和全球 Azure 租户设置。 收集关于 Azure Stack Hub 的必需信息。 将所需资源添加到租户和 Azure Stack Hub 市场。 群集依赖于 Ubuntu 服务器、自定义脚本以及要发布到 Azure Stack Hub 市场中的 Kubernetes 群集市场项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

为 Kubernetes 市场项创建计划、套餐和订阅。 也可以使用现有计划和套餐。

1. 登录到管理员门户 `https://adminportal.local.azurestack.external`。

1. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack Hub 中创建计划](azure-stack-create-plan.md)。

1. 创建套餐。 有关说明，请参阅[在 Azure Stack Hub 中创建套餐](azure-stack-create-offer.md)。

1. 选择“套餐”，并找到你创建的套餐。

1. 选择“套餐”边栏选项卡中的“概述”。

1. 选择“更改状态”。 选择“公共”。

1. 选择“+ 创建资源” > “套餐和计划” > “订阅”来创建订阅  。

    a. 输入 **显示名称**。

    b. 输入 **用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将“目录租户”设置为你的 Azure Stack Hub 的 Azure AD 租户。 

    e. 选择“套餐”。 选择你创建的套餐的名称。 记下订阅 ID。

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>在 AD FS 中创建服务主体和凭据

如果将 Active Directory 联合身份验证服务 (AD FS) 用于标识管理服务，则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端机密创建服务主体。 有关说明，请参阅[使用客户端机密创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到 Azure Stack Hub 市场：

1. 登录到管理员门户 `https://adminportal.local.azurestack.external`。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Ubuntu Server`。

1. 选择最新版本的服务器。 检查完整版本并确保已安装最新版本：
    - **发布者**：Canonical
    - **产品/服务**：UbuntuServer
    - **版本**：16.04.201806120（或最新版本）
    - **SKU**：16.04-LTS

1. 选择“下载”。

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从 Azure Stack Hub 市场添加 Kubernetes：

1. 打开管理员门户 `https://adminportal.local.azurestack.external`。

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

## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到市场

1. 打开管理员门户 `https://adminportal.local.azurestack.external`。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Kubernetes`。

1. 选择 `Kubernetes Cluster`。

1. 选择“下载”。

    > [!note]  
    > 可能需要等待五分钟，市场项才会显示在 Azure Stack Hub 市场中。

    ![Azure Stack Hub 市场中的 Kubernetes 项](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes

更新 Kubernetes 项时，将删除 Azure Stack Hub 市场中的上一项。 按照下面的说明将 Kubernetes 更新添加到 Azure Stack Hub 市场。

若要删除 Kubernetes 项，请执行以下操作：

1. 以操作员身份使用 PowerShell 连接到 Azure Stack Hub。 有关说明，请参阅[以操作员身份使用 PowerShell 连接到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)。

2. 在库中查找当前的 Kubernetes 群集项。

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记下当前项的名称，例如 `Microsoft.AzureStackKubernetesCluster.0.3.0`。

4. 使用以下 PowerShell cmdlet 删除项：

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack Hub](../user/azure-stack-solution-template-kubernetes-deploy.md)

[在 Azure Stack Hub 中提供服务概述](service-plan-offer-subscription-overview.md)
