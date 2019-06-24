---
title: 将 Kubernetes 添加到 Azure Stack 市场 | Microsoft Docs
description: 了解如何将 Kubernetes 添加到 Azure Stack 市场。
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
ms.openlocfilehash: 61d2739475a0593671e7a363671dd2859a6e6f24
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316253"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>将 Kubernetes 添加到 Azure Stack 市场

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure Stack 上的 Kubernetes 现为预览版。 在预览目前不支持 Azure Stack 离线的场景。 仅使用 marketplace 项进行开发和测试方案。

可以将 Kubernetes 作为市场项提供给用户。 然后，用户可以将 Kubernetes 部署在单个协调的操作。

下面的文章着眼于使用 Azure 资源管理器模板为独立的 Kubernetes 群集部署和预配资源。 在开始之前，请检查 Azure Stack 和全球 Azure 租户设置。 收集关于 Azure Stack 的必需信息。 将所需资源添加到租户和 Azure Stack 市场。 群集取决于 Ubuntu 服务器、 自定义脚本，以及要在 marketplace 中的 Kubernetes 群集 Marketplace 项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

为 Kubernetes 市场项创建计划、套餐和订阅。 也可以使用现有计划和套餐。

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack 中创建计划](azure-stack-create-plan.md)。

1. 创建套餐。 有关说明，请参阅[在 Azure Stack 中创建套餐](azure-stack-create-offer.md)。

1. 选择“套餐”  ，并找到你创建的套餐。

1. 选择“套餐”边栏选项卡中的“概述”。 

1. 选择“更改状态”。  选择“公共”。 

1. 选择 **+ 创建资源** > **提供了和计划** > **订阅**创建订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将“目录租户”  设置为你的 Azure Stack 的 Azure AD 租户。 

    e. 选择“套餐”。  选择你创建的套餐的名称。 记下订阅 ID。

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>在 AD FS 中创建服务主体和凭据

如果您使用 Active Directory 联合身份验证服务 (AD FS) 标识管理服务，需要创建服务主体的用户部署 Kubernetes 群集。 创建服务主体使用的客户端机密。 有关说明，请参阅[创建服务主体使用的客户端机密](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到市场：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”  ，然后在“管理”  类别下，选择“市场管理”  。

1. 选择“+ 从 Azure 添加”。 

1. 输入 `Ubuntu Server` 。

1. 选择最新版本的服务器。 检查完整版本并确保已安装最新版本：
    - **发布者**：Canonical
    - **产品/服务**：UbuntuServer
    - **版本**：16.04.201806120 （或最新版本）
    - **SKU**：16.04-LTS

1. 选择“下载”  。

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从市场中添加 Kubernetes：

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”  ，然后在“管理”  类别下，选择“市场管理”  。

1. 选择“+ 从 Azure 添加”。 

1. 输入 `Custom Script for Linux` 。

1. 选择具有以下配置文件的服务器：
   - **产品/服务**：适用于 Linux 的自定义脚本 2.0
   - **版本**：2.0.6 （或最新版本）
   - **发布者**：Microsoft Corp

     > [!Note]  
     > 可能会列出适用于 Linux 的自定义脚本的多个版本。 你将需要添加的项的最后一个版本。

1. 选择“下载”  。


## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到市场

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”  ，然后在“管理”  类别下，选择“市场管理”  。

1. 选择“+ 从 Azure 添加”。 

1. 输入 `Kubernetes` 。

1. 选择 `Kubernetes Cluster`。

1. 选择“下载”  。

    > [!note]  
    > 市场项可能需要花费五分钟才会出现在市场中。

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes 

当更新 Kubernetes 项，则会在 Marketplace 中删除上一项。 按照这篇文章中的说明将添加到 marketplace 更新 Kubernetes。

若要删除 Kubernetes 项，请执行以下操作：

1. 以操作员身份使用 PowerShell 连接到 Azure Stack。 有关说明，请参阅[以操作员身份使用 PowerShell 连接到 Azure Stack](azure-stack-powershell-configure-admin.md)。

2. 在库中查找当前的 Kubernetes 群集项。

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记下当前项的名称，例如 `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. 使用以下 PowerShell cmdlet 删除项：

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[在 Azure Stack 中提供服务概述](azure-stack-offer-services-overview.md)
