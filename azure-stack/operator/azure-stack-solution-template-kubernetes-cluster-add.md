---
title: 将 Kubernetes 添加到 Azure Stack Marketplace
titleSuffix: Azure Stack
description: 了解如何将 Kubernetes 添加到 Azure Stack Marketplace。
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
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 985d0e33fd5a15329a1a47bd2d6b11e50cd82a1c
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780807"
---
# <a name="add-kubernetes-to-azure-stack-marketplace"></a>将 Kubernetes 添加到 Azure Stack Marketplace

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> 仅使用 Kubernetes Azure Stack Marketplace 项将群集部署为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用[AKS 引擎](azure-stack-aks-engine.md)。

你可以向用户提供 Kubernetes 作为 marketplace 项。 然后，用户可以通过单个协调的操作来部署 Kubernetes。

本文介绍如何使用 Azure 资源管理器模板来部署和预配独立 Kubernetes 群集的资源。 在开始之前，请检查 Azure Stack 和全局 Azure 租户设置。 收集有关 Azure Stack 的所需信息。 向租户添加必要的资源并 Azure Stack Marketplace。 群集依赖于 Ubuntu 服务器、自定义脚本和 Kubernetes 群集 marketplace 项 Azure Stack Marketplace。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、产品/服务和订阅

为 Kubernetes marketplace 项创建计划、产品/服务和订阅。 你还可以使用现有计划和产品/服务。

1. 登录到[管理员门户。](https://adminportal.local.azurestack.external)

1. 创建计划作为基本计划。 有关说明，请参阅[在 Azure Stack 中创建计划](azure-stack-create-plan.md)。

1. 创建产品/服务。 有关说明，请参阅[在 Azure Stack 中创建产品/服务](azure-stack-create-offer.md)。

1. 选择 "**产品/服务**"，并找到已创建的产品/服务。

1. 选择 **"** 产品/服务" 边栏选项卡。

1. 选择 "**更改状态**"。 选择“公共”。

1. 选择 " **+ 创建资源**" > **提供并计划** > **订阅**以创建订阅。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 输入**显示名称**。

    b. 输入**用户**。 使用与租户关联的 Azure AD 帐户。

    c. **提供程序说明**

    d.单击“下一步”。 将**目录租户**设置为 Azure Stack 的 Azure AD 租户。 

    e. 选择 "**产品/服务**"。 选择您创建的产品/服务的名称。 记下订阅 ID。

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>在 AD FS 中创建服务主体和凭据

如果你为标识管理服务使用 Active Directory 联合服务（AD FS），则需要为部署 Kubernetes 群集的用户创建服务主体。 使用客户端密码创建服务主体。 有关说明，请参阅[使用客户端密码创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu server 映像

将以下 Ubuntu 服务器映像添加到 Azure Stack Marketplace：

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。

1. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 " **Marketplace 管理**"。

1. 选择 " **+ 从 Azure 添加**"。

1. 输入 `Ubuntu Server` 。

1. 选择最新版本的服务器。 查看完整版本，并确保具有最新版本：
    - **发布者**：规范
    - **产品/服务**： UbuntuServer
    - **版本**：16.04.201806120 （或最新版本）
    - **SKU**： 16.04-LTS

1. 选择 "**下载"。**

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

添加 Azure Stack Marketplace 中的 Kubernetes：

1. 打开[管理员门户](https://adminportal.local.azurestack.external)。

1. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 " **Marketplace 管理**"。

1. 选择 " **+ 从 Azure 添加**"。

1. 输入 `Custom Script for Linux` 。

1. 选择包含以下配置文件的脚本：
   - **产品/服务**：适用于 Linux 2.0 的自定义脚本
   - **版本**：2.0.6 （或最新版本）
   - **发布者**： Microsoft Corp

     > [!Note]  
     > 可能列出了适用于 Linux 的自定义脚本的多个版本。 需要添加项的最后一个版本。

1. 选择 "**下载"。**

## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到 marketplace

1. 打开[管理员门户](https://adminportal.local.azurestack.external)。

1. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 " **Marketplace 管理**"。

1. 选择 " **+ 从 Azure 添加**"。

1. 输入 `Kubernetes` 。

1. 选择 `Kubernetes Cluster`。

1. 选择 "**下载"。**

    > [!note]  
    > Marketplace 项可能需要五分钟才能出现在 Azure Stack Marketplace 中。

    ![Azure Stack Marketplace 中的 Kubernetes 项](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes

更新 Kubernetes 项时，Azure Stack Marketplace 中删除上一项。 按照以下说明将 Kubernetes 更新添加到 Azure Stack Marketplace。

删除 Kubernetes 项：

1. 使用 PowerShell 作为操作员连接到 Azure Stack。 有关说明，请参阅[使用 PowerShell 作为操作员连接到 Azure Stack](azure-stack-powershell-configure-admin.md)。

2. 查找库中的当前 Kubernetes 群集项。

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记录当前项的名称，如 `Microsoft.AzureStackKubernetesCluster.0.3.0`。

4. 使用以下 PowerShell cmdlet 删除该项：

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[在 Azure Stack 中提供服务概述](service-plan-offer-subscription-overview.md)
