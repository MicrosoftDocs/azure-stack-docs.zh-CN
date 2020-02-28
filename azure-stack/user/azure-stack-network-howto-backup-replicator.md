---
title: 如何跨多个 Azure Stack 中心订阅复制资源
description: 了解如何使用 Azure Stack 中心订阅复制器集复制资源。
author: mattbriggs
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: b8e955075553480b4178223a5475b44e3debd106
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702340"
---
# <a name="replicate-resources-using-the-azure-stack-hub-subscription-replicator"></a>使用 Azure Stack 中心订阅复制器复制资源

你可以使用 Azure Stack 中心订阅复制器 PowerShell 脚本在 Azure Stack 中心订阅之间、Azure Stack 中心戳记之间或 Azure Stack 中心和 Azure 之间复制资源。 复制器脚本从不同的 Azure 和 Azure Stack 中心订阅中读取和重新生成 Azure 资源管理器资源。 本文介绍脚本的工作原理、如何使用脚本，并提供脚本操作的参考。

可以在[Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)GitHub 存储库中找到本文中使用的脚本。 脚本位于 "[订阅复制](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/subscription%20replicator)器" 文件夹中。

## <a name="subscription-replicator-overview"></a>订阅复制器概述

Azure 订阅复制器设计为模块化。 此工具使用核心处理器来协调资源复制。 此外，该工具支持可自定义的处理器，它们充当用于复制不同类型资源的模板。 

核心处理器由以下三个脚本组成：

- **resource_retriever ps1**

    - 生成用于存储输出文件的文件夹。

    - 将上下文设置为源订阅。

    - 检索资源并将其传递到**resource_processor ps1**。

- **resource_processor ps1**

    - 通过**resource_retriever ps1**处理传入的资源。

    - 确定要使用的自定义处理器并传递资源。

- **post_process ps1**

    - Post 处理自定义处理器生成的输出，以准备将其部署到目标订阅中。

    - 生成部署代码以部署目标订阅中的资源。

这三个脚本以标准的方式控制信息流，以实现更大的灵活性。 例如，添加对其他资源的支持不需要更改核心处理器中的任何代码。

上面提到的自定义处理器是 `ps1` 的文件，用于决定如何处理某种类型的资源。 自定义处理器的名称始终使用资源中的类型数据进行命名。 例如，假设 `$vm` 包含运行 `$vm`的虚拟机对象。类型会产生 `Microsoft.Compute/virtualMachines`。 这意味着，虚拟机的处理器将命名为 `virtualMachines_processor.ps1`，该名称必须与在资源元数据中显示的名称完全相同，因为内核处理器如何确定要使用的自定义处理器。

自定义处理器通过确定哪些信息非常重要，并决定应如何从资源元数据中提取信息，来确定应如何复制资源。 自定义的处理器会获取所有提取的数据并使用它来生成参数文件，该文件将与 Azure 资源管理器模板结合使用，以在目标订阅中部署资源。 Post_process ps1 处理后，此参数文件存储在**Parameter_Files**中。

复制器文件结构中有一个名为 " **Standardized_ARM_Templates**" 的文件夹。 根据源环境，部署将使用这些标准化的 Azure 资源管理器模板之一，或者必须生成自定义的 Azure 资源管理器模板。 在这种情况下，自定义处理器必须调用 Azure 资源管理器模板生成器。 在前面的示例中，虚拟机的 Azure 资源管理器模板生成器的名称将命名为**virtualMachines_ARM_Template_Generator ps1**。 Azure 资源管理器模板生成器负责根据资源的元数据中的信息创建自定义的 Azure 资源管理器模板。 例如，如果虚拟机资源的元数据指定它是可用性集的成员，则 Azure 资源管理器模板生成器将创建一个 Azure 资源管理器模板，该模板的代码指定了虚拟机为的一部分。 这样，在将虚拟机部署到新的订阅时，它会自动在部署时添加到可用性集。 这些自定义的 Azure 资源管理器模板存储在位于**Standardized_ARM_Templates**文件夹内的**Custom_ARM_Templates**文件夹中。 Post_processor ps1 负责确定部署是否应该使用标准化的 Azure 资源管理器模板或自定义的部署代码并生成相应的部署代码。

脚本**post-process**负责清理参数文件，并创建用户将用于部署新资源的脚本。 在清理阶段，该脚本会将对源订阅 ID、租户 ID 和位置的所有引用替换为相应的目标值。 然后，它会将参数文件输出到**Parameter_Files**文件夹。 然后，它会确定正在处理的资源是使用自定义的 Azure 资源管理器模板还是不生成相应的部署代码，这将使用**new-azurermresourcegroupdeployment** cmdlet。 然后，将部署代码添加到**Deployment_Files**文件夹中存储的名为**DeployResources**的文件中。 最后，该脚本确定资源所属的资源组，并检查**DeployResourceGroups**脚本，以查看部署代码部署该资源组是否已存在。 如果不是，则它将向该脚本添加代码以部署资源组，如果这样，则不执行任何操作。

### <a name="dynamic-api-retrieval"></a>动态 API 检索

此工具内置了动态 API 检索功能，以便使用源订阅中提供的最新资源提供程序 API 版本来部署目标订阅中的资源：

![图 API 检索](./media/azure-stack-network-howto-backup-replicator/image1.png)

图**resource_processor ps1**中的 API 检索。

但是，目标订阅的资源提供程序 API 版本比源订阅的版本旧，并且不支持从源订阅提供的版本。 在这种情况下，将在运行部署时引发错误。 若要解决此问题，请更新目标订阅中的资源提供程序，使其与源订阅中的资源提供程序匹配。

### <a name="parallel-deployments"></a>并行部署

该工具需要一个名为**parallel**的参数。 此参数采用布尔值，指定是否应并行部署检索到的资源。 如果将该值设置为**true，** 则每次调用**new-azurermresourcegroupdeployment**时，将在基于资源类型的资源部署集之间添加 **-asJob**标志，并将代码块添加到要等待的并行作业完成。 它可确保在部署下一种类型的资源之前部署一种类型的所有资源。 如果将**parallel**参数值设置为**false**，则将所有资源都部署在串行中。

## <a name="add-additional-resource-types"></a>添加其他资源类型

添加新的资源类型非常简单。 开发人员必须创建自定义的处理器，并使用 Azure 资源管理器模板或 Azure 资源管理器模板生成器。 完成后，开发人员必须将该资源类型添加到 **$resourceType**参数的 ValidateSet 和 resource_retriever ps1 中的 **$resourceTypes**数组。 将资源类型添加到 * * $resourceTypes * * 数组时，必须按正确的顺序添加。 数组的顺序决定了将部署资源的顺序，因此请记住依赖关系。 最后，如果自定义的处理器使用 Azure 资源管理器模板生成器，则必须将资源类型名称添加到**post_process ps1**中的 **$customTypes**数组。

## <a name="run-azure-subscription-replicator"></a>运行 Azure 订阅复制器

若要运行 Azure 订阅复制器（v3）工具，需要启动 resource_retriever ps1，并提供所有参数。 **ResourceType**参数提供了一个选项，用于选择**所有**而不是一种资源类型。 如果选择 "**全部**"，则 resource_retriever ps1 将按顺序处理所有资源，以便在运行部署时，首先部署依赖的资源。 例如，在虚拟机之前部署 Vnet，因为虚拟机需要一个 VNet 才能正确部署。

脚本执行完毕后，将有三个新文件夹， **Deployment_Files**、 **Parameter_Files**和**Custom_ARM_Templates**。

 > [!Note]  
 > 在运行任何生成的脚本之前，必须设置正确的环境，并登录到目标订阅（对于 ex，在新的 Azure Stack 中心中），并将工作目录设置为**Deployment_Files**文件夹。

Deployment_Files 将保存两个**DeployResourceGroups**和**DeployResources**文件。 执行 DeployResourceGroups 将部署资源组。 执行 DeployResources 将部署已处理的所有资源。 如果该工具是使用**All**或**Microsoft. Compute/virtualMachines**作为资源类型执行的，则 DeployResources 会提示用户输入将用于创建所有虚拟机的虚拟机管理员密码。

### <a name="example"></a>示例

1.  运行该脚本。

    ![运行脚本](./media/azure-stack-network-howto-backup-replicator/image2.png)

    > [!Note]  
    > 别忘了为 PS 实例配置源环境和订阅上下文。 

2.  查看新创建的文件夹：

    ![查看文件夹](./media/azure-stack-network-howto-backup-replicator/image4.png)

3.  将上下文设置为目标订阅，将文件夹更改为**Deployment_Files**，部署资源组，然后启动资源部署。

    ![配置并启动部署](./media/azure-stack-network-howto-backup-replicator/image6.png)

4.  运行 `Get-Job` 以检查状态。 获取-作业 |接收-作业将返回结果。

## <a name="clean-up"></a>清除

在 replicatorV3 文件夹中，有一个名为**cleanup_generated_items. ps1**的文件-它将删除**Deployment_Files**、 **Parameter_Files**和**Custom_ARM_Templates**文件夹及其所有内容。

## <a name="subscription-replicator-operations"></a>订阅复制器操作

Azure 订阅复制器（v3）当前可以复制以下资源类型：

- Microsoft.Compute/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft.Network/loadBalancers

- Microsoft.Network/networkSecurityGroups

- Microsoft.Network/publicIPAddresses

- RouteTables/网络

- Microsoft.Network/virtualNetworks

- Microsoft.Network/virtualNetworkGateways

- Microsoft.Storage/storageAccounts

当使用**全部**作为资源类型运行工具时，将按照以下顺序进行复制和部署（在下面的内容中，所有资源均已复制其配置，即 sku、产品/服务等）：

- Microsoft.Network/virtualNetworks

    - 复制：-所有地址空间-所有子网

- Microsoft.Network/virtualNetworkGateways

    - 复制：-公共 IP 配置-子网配置-VPN 类型-网关类型

- RouteTables/网络

- Microsoft.Network/networkSecurityGroups

    - 复制：-所有安全规则入站和出站

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/loadBalancers

    - 复制：-专用 IP 地址-公共 IP 地址配置-子网配置
    
- Microsoft.Compute/availabilitySets

    - 复制：-容错域的数目-更新域的数目

- Microsoft.Storage/storageAccounts

- Microsoft.Compute/virtualMachines
    - 复制  
            -数据磁盘（无数据）  
            -虚拟机大小  
            -操作系统  
            -诊断存储帐户配置  
            -公共 IP 配置  
            -网络接口  
            -网络接口专用 IP 地址  
            -网络安全组配置  
            -可用性集配置  

> [!Note]  
> 只为 OS 磁盘和数据磁盘创建托管磁盘。 当前不支持使用存储帐户 

### <a name="limitations"></a>限制

此工具可以将资源从一个订阅复制到另一个订阅，只要目标订阅的资源提供程序支持从源订阅复制的所有资源和选项。

为了确保复制成功，mare 确保目标订阅的资源提供程序版本与源订阅的资源提供程序版本相匹配。

在从商业 Azure 复制到商业 Azure 或从 Azure Stack 中心内的一个订阅复制到相同 Azure Stack 中心内的另一个订阅时，将在复制存储帐户时出现问题。 这是因为存储帐户命名要求所有存储帐户名称在所有商业 Azure 中都是唯一的，或是在 Azure Stack 中心区域/实例上的所有订阅中都是唯一的。 跨不同 Azure Stack 中心实例复制存储帐户将会成功，因为堆栈是单独的区域/实例。



## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)  
