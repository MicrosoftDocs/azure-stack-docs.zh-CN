---
title: 在 Azure Stack Hub 上的应用服务中添加辅助角色和基础结构
description: Azure Stack 中心的缩放 Azure App Service 的详细指南
author: bryanla
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: 346b55e867d5b6e21ac1004e9df5925c3cf01d0b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876484"
---
# <a name="add-workers-and-infrastructure-in-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的 Azure App Service 中添加辅助角色和基础结构

本文档提供有关如何在 Azure Stack 中心的 Azure App Service 中缩放基础结构和辅助角色的说明。 我们将介绍创建附加辅助角色以支持任意大小的应用程序所需的所有步骤。

> [!NOTE]
> 如果 Azure Stack 集线器环境不超过 96 GB 的 RAM，则可能会遇到增加额外容量的问题。

默认情况下，Azure App Service Azure Stack 中心支持可用和共享的辅助角色层。 若要添加其他辅助角色层，需要添加更多辅助角色。

如果你不确定在 Azure Stack 中心安装上安装了默认 Azure App Service 的内容，则可以在[Azure Stack 集线器概述中查看应用服务](azure-stack-app-service-overview.md)的其他信息。

Azure Stack 中心的 Azure App Service 使用虚拟机规模集部署所有角色，因而利用了此工作负荷的缩放功能。 因此，辅助角色层的所有缩放都是通过应用服务管理员来完成的。

## <a name="add-additional-workers-with-powershell"></a>通过 PowerShell 添加其他辅助角色

1. [在 PowerShell 中设置 Azure Stack 集线器管理员环境](azure-stack-powershell-configure-admin.md)

2. 使用此示例扩展规模集：
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
   > 此步骤可能需要几个小时才能完成，具体取决于角色类型和实例数。
   >
   >

3. 在应用服务管理中监视新角色实例的状态。 若要检查单个角色实例的状态，请在列表中单击该角色类型。

## <a name="add-additional-workers-using-the-administrator-portal"></a>使用管理员门户添加其他辅助角色

1. 以服务管理员身份登录到 Azure Stack 中心管理员门户。

2. 浏览到 "**应用服务**"。

    ![Azure Stack 中心管理员门户中的应用服务](media/azure-stack-app-service-add-worker-roles/image01.png)

3. 单击“角色”。 可在此处查看已部署的所有应用服务角色的细分。

4. 右键单击要缩放的类型的行，然后单击 "**规模集**"。

    ![Azure Stack 中心管理员门户中的规模集应用服务角色](media/azure-stack-app-service-add-worker-roles/image02.png)

5. 单击 "**缩放**"，选择要缩放到的实例数，然后单击 "**保存**"。

    ![在 Azure Stack 中心管理员门户中的应用服务角色中设置要缩放的实例](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure Stack 中心的 Azure App Service 现在将添加更多的 Vm、对其进行配置、安装所有必需的软件，并在此过程完成时将其标记为 "就绪"。 此过程可能需要大约80分钟。

7. 可以通过在 "**角色**" 边栏选项卡中查看辅助角色来监视新角色的准备情况。

## <a name="result"></a>结果

它们完全部署并准备就绪后，用户就可以将工作负荷部署到它们。 以下屏幕截图显示了默认情况下可用的多个定价层的示例。 如果某个特定辅助角色层没有可用的辅助角色，则选择相应定价层的选项将不可用。

![Azure Stack 中心管理员门户中新应用服务计划的定价层](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 若要扩大管理、前端或发布者角色，请执行相同的步骤来选择适当的角色类型。 控制器不会部署为规模集，因此应在安装时为所有生产部署部署两个控制器。

### <a name="next-steps"></a>后续步骤

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)
