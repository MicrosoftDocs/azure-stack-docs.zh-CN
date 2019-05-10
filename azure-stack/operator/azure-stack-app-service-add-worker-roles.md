---
title: 横向扩展应用服务中的辅助角色 - Azure Stack | Microsoft Docs
description: Azure Stack 应用服务伸缩详细指南
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: d693a7f6a5cbe7d818dedcc664cc8846bcebc11c
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172475"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Azure Stack 上的应用服务：添加更多基础结构或辅助角色

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

本文档说明如何在 Azure Stack 基础结构和辅助角色上缩放应用服务。 本文档包含的步骤介绍了如何创建额外的辅助角色来支持任意大小的应用程序。

> [!NOTE]
> 如果 Azure Stack 环境没有 96 GB 以上的 RAM，则可能难以添加更多的容量。

基于 Azure Stack 的应用服务默认支持免费的和共享的辅助角色层。 若要添加其他辅助角色层，需添加更多的辅助角色。

如果不确定默认的基于 Azure Stack 的应用服务安装的部署内容，可以参阅[基于 Azure Stack 的应用服务概述](azure-stack-app-service-overview.md)以获取更多信息。

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

3. 在“应用服务管理”中监视新角色实例的状态。若要查看单个角色实例的状态，请单击列表中的角色类型。

## <a name="add-additional-workers-using-the-administration-portal"></a>使用管理门户添加更多辅助角色

1. 以服务管理员身份登录到 Azure Stack 管理门户。

2. 浏览到“应用服务”。

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. 单击“角色”。 在这里会看到所有已部署的应用服务角色的明细。

4. 右键单击要缩放的类型所在的行，然后单击“ScaleSet”。

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. 单击“缩放”，选择要缩放到的实例数，然后单击“保存”。

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. 基于 Azure Stack 的应用服务此时会添加其他 VM，对其进行配置，安装所有必需的软件，并在此过程完成后将其标记为“就绪”。 此过程可能需要大约 80 分钟。

7. 可以监视新角色就绪标记操作的进度，只需在“角色”边栏选项卡中查看辅助角色即可。

## <a name="result"></a>结果

在完全部署并就绪以后，辅助角色即可供用户使用，用户可以将其工作负荷部署到辅助角色上。 下面显示的示例为默认提供的多个定价层。 如果特定的辅助角色层没有可用的辅助角色，则用于选择相应定价层的选项不可用。

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 若要横向扩展“管理”、“前端”或“发布者”角色，必须横向扩展相应的角色类型。 
>
>

若要横向扩展“管理”、“前端”或“发布者”角色，请执行选择相应角色类型时执行的步骤。 控制器不是作为规模集来部署的，因此应该在安装时部署两个，这适用于所有生产部署。

### <a name="next-steps"></a>后续步骤

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)
