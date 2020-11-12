---
title: 跨多个 Azure Stack 中心订阅复制资源
description: 了解如何使用 Azure Stack Hub 订阅复制器脚本集复制资源。
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 80200b283ba6ef0266513eefaa1fdcb8faf9faa8
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546729"
---
# <a name="replicate-resources-using-the-azure-stack-hub-subscription-replicator"></a>使用 Azure Stack Hub 订阅复制器复制资源

可以使用 Azure Stack Hub 订阅复制器 PowerShell 脚本，在 Azure Stack Hub 订阅之间、跨 Azure Stack Hub 阵列或者在 Azure Stack Hub 与 Azure 之间复制资源。 复制器脚本从不同的 Azure 和 Azure Stack Hub 订阅读取和重建 Azure 资源管理器资源。 本文将介绍脚本的工作原理及其用法，并提供脚本操作的参考信息。

可以在 [Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub 存储库中找到本文中使用的脚本。 脚本位于[订阅复制器](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/subscription%20replicator)文件夹中。

## <a name="subscription-replicator-overview"></a>订阅复制器概述

Azure 订阅复制器采用模块化设计。 此工具使用核心处理器来协调资源的复制。 此外，此工具还支持将可自定义的处理器用作模板来复制不同类型的资源。 

核心处理器由以下三个脚本组成：

- **resource_retriever.ps1**

    - 生成用于存储输出文件的文件夹。

    - 将上下文设置为源订阅。

    - 检索资源并将其传递给 **resource_processor.ps1** 。

- **resource_processor.ps1**

    - 处理 **resource_retriever.ps1** 传入的资源。

    - 确定要使用的自定义处理器，并传递资源。

- **post_process.ps1**

    - 对自定义处理器生成的输出进行后处理，使其准备好部署到目标订阅中。

    - 生成部署代码，以将资源部署到目标订阅中。

这三个脚本以标准方式控制信息的流动，以提高灵活性。 例如，无需更改核心处理器中的任何代码即可添加对其他资源的支持。

上述自定义处理器是规定特定资源类型的处理方式的 `ps1` 文件。 始终使用资源中的类型数据来为自定义处理器命名。 例如，假设 `$vm` 包含虚拟机对象，则运行 `$vm`.Type 会生成 `Microsoft.Compute/virtualMachines`。 也就是说，虚拟机的处理器将命名为 `virtualMachines_processor.ps1`，该名称必须与资源元数据中显示的名称完全相同，因为核心处理器以这种方式确定要使用的自定义处理器。

自定义处理器会确定哪些信息很重要，并指定要如何从资源元数据提取这些信息，以此规定资源的复制方式。 然后，自定义处理器将获取所有已提取的数据，并使用这些数据来生成参数文件，将此文件与 Azure 资源管理器模板配合使用可将资源部署到目标订阅中。 经 post_process.ps1 后处理之后，此参数文件将存储在 **Parameter_Files** 中。

在复制器文件结构中，有一个名为 **Standardized_ARM_Templates** 的文件夹。 根据源环境，部署将使用其中一个已标准化的 Azure 资源管理器模板，否则必须生成自定义的 Azure 资源管理器模板。 在此情况下，自定义处理器必须调用 Azure 资源管理器模板生成器。 在前面所述的示例中，虚拟机的 Azure 资源管理器模板生成器命名为 **virtualMachines_ARM_Template_Generator.ps1** 。 Azure 资源管理器模板生成器负责根据资源元数据中的信息创建自定义的 Azure 资源管理器模板。 例如，如果虚拟机资源的元数据指定该资源本身是可用性集的成员，则 Azure 资源管理器模板生成器将创建 Azure 资源管理器模板，其中包含用于指定虚拟机所属可用性集的 ID 的代码。 这样，在将虚拟机部署到新订阅时，虚拟机将在部署后自动添加到该可用性集。 这些自定义的 Azure 资源管理器模板存储在 **Standardized_ARM_Templates** 文件夹中的 **Custom_ARM_Templates** 文件夹内。 post_processor.ps1 负责确定部署是要使用已标准化的 Azure 资源管理器模板还是自定义的模板，并生成相应的部署代码。

**post-process.ps1** 脚本负责清理参数文件，并创建供用户用来部署新资源的脚本。 在清理阶段，该脚本会将对源订阅 ID、租户 ID 和位置的所有引用替换为相应的目标值。 然后，该脚本将参数文件输出到 **Parameter_Files** 文件夹。 然后，它会确定正在处理的资源是使用自定义的 Azure 资源管理器模板还是不生成相应的部署代码，这将使用 **AzResourceGroupDeployment** cmdlet。 部署代码随后会添加到存储在 **Deployment_Files** 文件夹中的名为 **DeployResources.ps1** 的文件。 最后，该脚本确定资源所属的资源组，并检查 **DeployResourceGroups.ps1** 脚本，以确定用于部署该资源组的部署代码是否已存在。 如果不存在，则将代码添加到该脚本中以部署资源组；如果存在，则不执行任何操作。

### <a name="dynamic-api-retrieval"></a>动态 API 检索

该工具内置了动态 API 检索，让用户使用源订阅中可用的最新资源提供程序 API 版本在目标订阅中部署资源：

![数字 API 检索](./media/azure-stack-network-howto-backup-replicator/image1.png)

**resource_processor.ps1** 中的数字 API 检索。

但是，目标订阅的资源提供程序 API 版本有可能比源订阅的资源提供程序 API 版本要低，且不支持源订阅所提供的版本。 在此情况下，运行部署时会引发错误。 若要解决此问题，请更新目标订阅中的资源提供程序，使之与源订阅中的资源提供程序相匹配。

### <a name="parallel-deployments"></a>并行部署

该工具需要名为 **parallel** 的参数。 此参数采用布尔值，指定是否应该以并行方式部署检索到的资源。 如果将该值设置为 **true，** 则每次调用 **AzResourceGroupDeployment** 时，将在基于资源类型的资源部署集之间添加 **-asJob** 标志，并将代码块添加到要等待的并行作业完成。 这可以确保在某种类型的所有资源都已部署完成后，才部署下一种类型的资源。 如果 **parallel** 参数值设置为 **false** ，则会连续部署所有资源。

## <a name="add-additional-resource-types"></a>添加其他资源类型

添加资源类型的过程很简单。 开发人员必须创建自定义处理器，以及 Azure 资源管理器模板或 Azure 资源管理器模板生成器。 上述操作完成后，开发人员还必须将资源类型添加到 **$resourceType** 参数的 ValidateSet，以及 resource_retriever.ps1 中的 **$resourceTypes** 数组。 将资源类型添加到 **$resourceTypes** 数组时，必须按正确的顺序添加。 数组顺序确定资源的部署顺序，因此要考虑到依赖项。 最后，如果自定义处理器使用 Azure 资源管理器模板生成器，则必须将资源类型名称添加到 **post_process.ps1** 中的 **$customTypes** 数组。

## <a name="run-azure-subscription-replicator"></a>运行 Azure 订阅复制器

若要运行 Azure 订阅复制器 (v3) 工具，必须启动 resource_retriever.ps1，并提供所有参数。 在 **resourceType** 参数中，有一个选项可用于选择“All”而不是一种资源类型。 如果选择“All”，resource_retriever.ps1 将按某种顺序处理所有资源，以便在运行部署时首先部署依赖的资源。 例如，先部署 VNet，再部署虚拟机，因为虚拟机需要 VNet 准备就绪才能正确部署。

脚本运行完成后，会出现三个新文件夹： **Deployment_Files** 、 **Parameter_Files** 和 **Custom_ARM_Templates** 。

 > [!NOTE]  
 > 在运行任何已生成的脚本之前，必须先设置正确的环境并登录到目标订阅（例如，在新的 Azure Stack Hub 中），然后将工作目录设置为 **Deployment_Files** 文件夹。

Deployment_Files 包含两个文件： **DeployResourceGroups.ps1** 和 **DeployResources.ps1** 。 执行 DeployResourceGroups.ps1 会部署资源组。 执行 DeployResources.ps1 会部署所有已处理的资源。 如果在使用 **All** 或 **Microsoft.Compute/virtualMachines** 作为资源类型的情况下运行该工具，DeployResources.ps1 将提示用户输入虚拟机管理员密码，以用于创建所有虚拟机。

### <a name="example"></a>示例

1.  运行该脚本。

    ![运行脚本](./media/azure-stack-network-howto-backup-replicator/image2.png)

    > [!NOTE]  
    > 别忘了为 PS 实例配置环境和订阅上下文。 

2.  查看新建的文件夹：

    ![查看文件夹](./media/azure-stack-network-howto-backup-replicator/image4.png)

3.  将上下文设置为目标订阅，将文件夹更改为 Deployment_Files，部署资源组（运行 DeployResourceGroups.ps1 脚本），然后启动资源部署（运行 DeployResources.ps1 脚本）。

    ![配置并启动部署](./media/azure-stack-network-howto-backup-replicator/image6.png)

4.  运行 `Get-Job` 以检查状态。 Get-Job | Receive-Job 将返回结果。

## <a name="clean-up"></a>清理

在 replicatorV3 文件夹中，有一个名为 **cleanup_generated_items.ps1** 的文件 - 该文件将删除 **Deployment_Files** 、 **Parameter_Files** 和 **Custom_ARM_Templates** 文件夹及其所有内容。

## <a name="subscription-replicator-operations"></a>订阅复制器操作

Azure 订阅复制器 (v3) 目前可以复制以下资源类型：

- Microsoft.Compute/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft.Network/loadBalancers

- Microsoft.Network/networkSecurityGroups

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/routeTables

- Microsoft.Network/virtualNetworks

- Microsoft.Network/virtualNetworkGateways

- Microsoft.Storage/storageAccounts

在使用 **All** 作为资源类型运行该工具时，复制和部署将按以下顺序进行（下面所有资源的配置都已复制，例如 SKU、套餐等）：

- Microsoft.Network/virtualNetworks

    - 复制：- 所有地址空间 - 所有子网

- Microsoft.Network/virtualNetworkGateways

    - 复制：- 公共 IP 配置 - 子网配置 - VPN 类型 - 网关类型

- Microsoft.Network/routeTables

- Microsoft.Network/networkSecurityGroups

    - 复制：- 所有入站和出站安全规则

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/loadBalancers

    - 复制：- 专用 IP 地址 - 公共 IP 地址配置 - 子网配置
    
- Microsoft.Compute/availabilitySets

    - 复制：- 容错域数目 - 更新域数目

- Microsoft.Storage/storageAccounts

- Microsoft.Compute/virtualMachines
    - 复制：  
            - 数据磁盘（无数据）  
            - 虚拟机大小  
            - 操作系统  
            - 诊断存储帐户配置  
            - 公共 IP 配置  
            - 网络接口  
            - 网络接口专用 IP 地址  
            - 网络安全组配置  
            - 可用性集配置  

> [!NOTE]  
> 仅为 OS 磁盘和数据磁盘创建托管磁盘。 目前不支持使用存储帐户 

### <a name="limitations"></a>限制

只要目标订阅的资源提供程序支持从源订阅复制的所有资源和选项，该工具就可将资源从一个订阅复制到另一个订阅。

为确保复制成功，请确保目标订阅的资源提供程序版本与源订阅的资源提供程序版本相匹配。

在从商用 Azure 复制到商用 Azure 或者从 Azure Stack Hub 内部的一个订阅复制到同一 Azure Stack Hub 内部的另一个订阅过程中，复制存储帐户时会出现问题。 原因是存储帐户命名要求规定，所有存储帐户名称在所有商用 Azure 中或 Azure Stack Hub 区域/实例的所有订阅中必须唯一。 跨不同的 Azure Stack Hub 实例复制存储帐户将会成功，因为 Azure Stack 是独立的区域/实例。



## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  
