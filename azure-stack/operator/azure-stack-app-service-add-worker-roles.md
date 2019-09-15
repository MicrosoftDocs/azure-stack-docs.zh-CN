---
title: 在 Azure Stack 上的应用服务中添加辅助角色和基础结构 |Microsoft Docs
description: Azure Stack 应用服务伸缩详细指南
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: b01199bfe96c39fe79aac65eca219a065f39375c
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975012"
---
# <a name="add-workers-and-infrastructure-in-app-service-on-azure-stack"></a>Azure Stack 上的应用服务中添加辅助角色和基础结构

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*  

本文档提供有关如何在 Azure Stack 的应用服务中缩放基础结构和辅助角色的说明。 我们将介绍创建附加辅助角色以支持任意大小的应用程序所需的所有步骤。

> [!NOTE]
> 如果 Azure Stack 环境不超过 96 GB 的 RAM，则可能会遇到增加额外容量的问题。

默认情况下，Azure Stack 上的应用服务支持免费和共享辅助角色层。 若要添加其他辅助角色层，需添加更多的辅助角色。

如果你不确定 Azure Stack 安装上的默认应用服务部署的内容，你可以[Azure Stack 概述查看应用服务](azure-stack-app-service-overview.md)中的其他信息。

基于 Azure Stack 的 Azure 应用服务使用虚拟机规模集来部署所有角色，因此可充分利用此工作负荷的缩放功能。 因此，辅助角色层的所有缩放都是通过应用服务管理员完成的。

## <a name="add-additional-workers-with-powershell"></a>使用 PowerShell 添加更多辅助角色

1. [在 PowerShell 中设置 Azure Stack 管理环境](azure-stack-powershell-configure-admin.md)

2. 使用以下示例来横向扩展规模集：
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > 根据角色类型和实例数目，此步骤可能需要几小时才能完成。
   >
   >

3. 在应用服务管理中监视新角色实例的状态。 若要检查单个角色实例的状态，请在列表中单击该角色类型。

## <a name="add-additional-workers-using-the-administrator-portal"></a>使用管理员门户添加其他辅助角色

1. 以服务管理员身份登录到 Azure Stack 管理员门户。

2. 浏览到“应用服务”。

    ![Azure Stack 管理员门户中的应用服务](media/azure-stack-app-service-add-worker-roles/image01.png)

3. 单击“角色”。 在这里会看到所有已部署的应用服务角色的明细。

4. 右键单击要缩放的类型所在的行，然后单击“ScaleSet”。

    ![Azure Stack 管理员门户中的规模集应用服务角色](media/azure-stack-app-service-add-worker-roles/image02.png)

5. 单击“缩放”，选择要缩放到的实例数，然后单击“保存”。

    ![在 Azure Stack 管理员门户中的应用服务角色中设置要缩放的实例](media/azure-stack-app-service-add-worker-roles/image03.png)

6. 基于 Azure Stack 的应用服务此时会添加其他 VM，对其进行配置，安装所有必需的软件，并在此过程完成后将其标记为“就绪”。 此过程可能需要大约 80 分钟。

7. 可以监视新角色就绪标记操作的进度，只需在“角色”边栏选项卡中查看辅助角色即可。

## <a name="result"></a>结果

它们完全部署并准备就绪后，用户就可以将工作负荷部署到它们。 以下屏幕截图显示了默认情况下可用的多个定价层的示例。 如果特定的辅助角色层没有可用的辅助角色，则用于选择相应定价层的选项不可用。

![Azure Stack 管理员门户中的新应用服务计划的定价层](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 若要横向扩展“管理”、“前端”或“发布者”角色，请执行选择相应角色类型时执行的步骤。 控制器不会部署为规模集，因此应在安装时为所有生产部署部署两个控制器。

### <a name="next-steps"></a>后续步骤

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)
