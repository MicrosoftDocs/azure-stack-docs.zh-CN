---
title: 将 Kubernetes 添加到 Azure Stack 中心市场
titleSuffix: Azure Stack Hub
description: 了解如何将 Kubernetes 添加到 Azure Stack 中心市场。
author: mattbriggs
ms.topic: article
ms.date: 03/24/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 03/24/2020v
ms.openlocfilehash: 092d73339eb2ea733cb0fb67404bf52cc9539343
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84630759"
---
# <a name="add-kubernetes-to-azure-stack-hub-marketplace"></a>将 Kubernetes 添加到 Azure Stack 中心市场

> [!note]  
> 仅使用 Kubernetes Azure Stack 中心市场项将群集部署为概念证明。 对于 Azure Stack 集线器上支持的 Kubernetes 群集，请使用[AKS 引擎](azure-stack-aks-engine.md)。

你可以向用户提供 Kubernetes 作为 marketplace 项。 然后，用户可以通过单个协调的操作来部署 Kubernetes。

本文介绍如何使用 Azure 资源管理器模板来部署和预配独立 Kubernetes 群集的资源。 在开始之前，请检查 Azure Stack 中心和全局 Azure 租户设置。 收集 Azure Stack 中心所需的信息。 向你的租户添加所需的资源，并 Azure Stack 中心市场。 群集依赖于 Ubuntu 服务器、自定义脚本和 Kubernetes 群集 marketplace 项 Azure Stack 中心市场。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

为 Kubernetes marketplace 项创建计划、产品/服务和订阅。 也可以使用现有计划和套餐。

1. 登录到管理员门户 `https://adminportal.local.azurestack.external` 。

1. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack Hub 中创建计划](azure-stack-create-plan.md)。

1. 创建套餐。 有关说明，请参阅[在 Azure Stack Hub 中创建产品/服务](azure-stack-create-offer.md)。

1. 选择“套餐”****，并找到你创建的套餐。

1. 选择“套餐”边栏选项卡中的“概述”。****

1. 选择“更改状态”。**** 选择“公共”。

1. 选择 " **+ 创建资源**" "  >  **产品/服务"，并计划**  >  **订阅**以创建订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将**目录租户**设置为 Azure Stack 中心的 Azure AD 租户。 

    e. 选择“套餐”。**** 选择你创建的套餐的名称。 记下订阅 ID。

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>在 AD FS 中创建服务主体和凭据

如果你为标识管理服务使用 Active Directory 联合服务（AD FS），则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端密码创建服务主体。 有关说明，请参阅[使用客户端密码创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到 Azure Stack 集线器 Marketplace：

1. 登录到管理员门户 `https://adminportal.local.azurestack.external` 。

1. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 " **Marketplace 管理**"。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Ubuntu Server`。

1. 选择最新版本的服务器。 检查完整版本并确保已安装最新版本：
    - **发布者**：Canonical
    - **套餐**：UbuntuServer
    - **版本**：16.04.201806120 （或最新版本）
    - **SKU**：16.04-LTS

1. 选择 "**下载"。**

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从 Azure Stack 中心市场添加 Kubernetes：

1. 打开管理员门户 `https://adminportal.local.azurestack.external` 。

1. 选择“所有服务”****，然后在“管理”**** 类别下，选择“市场管理”****。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Custom Script for Linux`。

1. 选择具有以下配置文件的服务器：
   - **套餐**：适用于 Linux 2.0 的自定义脚本
   - **版本**：2.0.6 （或最新版本）
   - **发布者**：Microsoft Corp

     > [!Note]  
     > 可能会列出适用于 Linux 的自定义脚本的多个版本。 需要添加项的最后一个版本。

1. 选择 "**下载"。**

## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到市场

1. 打开管理员门户 `https://adminportal.local.azurestack.external` 。

1. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 " **Marketplace 管理**"。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Kubernetes`。

1. 选择 `Kubernetes Cluster`。

1. 选择 "**下载"。**

    > [!note]  
    > Marketplace 项可能需要五分钟的时间才会出现在 Azure Stack Hub Marketplace 中。

    ![Azure Stack 中心 Marketplace 中的 Kubernetes 项](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes

更新 Kubernetes 项时，会删除 Azure Stack Hub Marketplace 中的上一项。 按照以下说明将 Kubernetes 更新添加到 Azure Stack 中心市场。

若要删除 Kubernetes 项，请执行以下操作：

1. 使用 PowerShell 作为操作员连接到 Azure Stack 集线器。 有关说明，请参阅[使用 PowerShell 作为操作员连接到 Azure Stack 集线器](azure-stack-powershell-configure-admin.md)。

2. 在库中查找当前的 Kubernetes 群集项。

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记录当前项的名称，如 `Microsoft.AzureStackKubernetesCluster.0.3.0` 。

4. 使用以下 PowerShell cmdlet 删除项：

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack 中心](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Azure Stack 中心提供服务的概述](service-plan-offer-subscription-overview.md)
