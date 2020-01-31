---
title: 管理 Azure Stack 集线器中的存储容量
description: 了解如何监视和管理 Azure Stack 集线器中的存储容量和可用性。
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 2d7db8ade21580af67c0a1b3f3783f694544f5e0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881994"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>管理 Azure Stack 集线器的存储容量

本文中的信息可帮助 Azure Stack 中心云操作员监视和管理其 Azure Stack 中心部署的存储容量。 Azure Stack 中心存储基础结构分配用于**存储服务**的 Azure Stack 中心部署的总存储容量的子集。 存储设备将租户的数据存储在与部署的节点对应的卷上的共享中。

作为云操作员，你可以使用有限数量的存储空间。 存储量由您实现的解决方案定义。 你的解决方案是在使用多节点解决方案时由你的 OEM 供应商提供的，或者是你在其上安装 Azure Stack 开发工具包的硬件（ASDK）。

由于 Azure Stack 集线器不支持存储容量的扩展，因此必须[监视](#monitor-shares)可用的存储，以确保保持有效的操作。

当共享的剩余可用容量有限时，计划[管理空间](#manage-available-space)以防止共享空间不足。

用于管理容量的选项包括：
- 回收容量
- 迁移容器

如果某个共享使用了100%，则存储服务不再可用于该共享。 若要获取有关还原共享操作的帮助，请联系 Microsoft 支持部门。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>了解卷和共享、容器和磁盘
### <a name="volumes-and-shares"></a>卷和共享
*存储服务*会将可用存储分区到分配用于保存租户数据的单独的和相等的卷。 卷数等于 Azure Stack 中心部署中的节点数：

- 在四节点部署中，有四个卷。 每个卷都有一个共享。 在多节点部署中，如果某个节点被删除或出现故障，共享的数量不会减少。
- 如果你使用 ASDK，则会有一个包含单个共享的卷。

由于存储服务共享用于独占使用存储服务，因此不能直接修改、添加或删除共享上的任何文件。 只有存储服务才能处理这些卷中存储的文件。

卷上的共享保存租户数据。 租户数据包括页 blob、块 blob、追加 blob、表、队列、数据库和相关的元数据存储区。 由于存储对象（blob 等）单独包含在单个共享内，因此每个对象的最大大小不能超过共享大小。 新的对象的最大大小取决于在创建新对象时，共享中保留为未使用空间的容量。

如果共享空间不足，并且[回收](#reclaim-capacity)空间的操作未成功或不可用，则 Azure Stack 中心云操作员可以将 blob 容器从一个共享迁移到另一个共享。

- 有关租户用户如何处理 Azure Stack 中心内的 blob 存储的信息，请参阅[Azure Stack 中心存储服务](/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services)。


### <a name="containers"></a>容器
租户用户创建用于存储 blob 数据的容器。 当用户决定放置 blob 的容器时，存储服务使用一种算法来确定放置容器的卷。 算法通常会选择具有最多可用空间的卷。  

将 blob 放置在容器中后，该 blob 可以增大以使用更多空间。 当你添加新的 blob 并且现有的 blob 增长时，保存该容器的卷中的可用空间将会缩小。  

容器并不局限于单个共享。 当容器中的组合 blob 数据增大到使用80% 或更多可用空间时，容器将进入*溢出*模式。 在溢出模式下，在该容器中创建的任何新 blob 都将分配给具有足够空间的其他卷。 随着时间的推移，溢出模式下的容器可以具有跨多个卷分布的 blob。

80% （再后90%）使用卷中的可用空间时，系统会在 Azure Stack 中心管理员门户中引发警报。 云操作员应查看可用的存储容量，并计划重新平衡内容。 当使用的磁盘为100% 且未引发其他警报时，存储服务将停止工作。

### <a name="disks"></a>磁盘
VM 磁盘会按租户添加到容器，并包含操作系统磁盘。 Vm 还可以有一个或多个数据磁盘。 这两种类型的磁盘都存储为页 blob。 租户的指导是将每个磁盘放置在单独的容器中，以提高 VM 的性能。

- 每个包含 VM 的磁盘（页 blob）的容器被视为拥有该磁盘的 VM 的附加容器。
- 未将任何磁盘保存到虚拟机（VM）的容器被视为可用容器。

用于释放附加容器上空间的选项[是有限](#move-vm-disks)的。

>[!TIP]  
> 云操作员不直接管理附加到租户可能添加到容器的 Vm 的磁盘。 但是，当计划管理存储共享空间时，可以使用它来了解磁盘与容器和共享的关系。

## <a name="monitor-shares"></a>监视共享
使用 PowerShell 或管理员门户监视共享，以便了解可用空间的限制。 使用门户时，会收到有关空间不足的共享的警报。

### <a name="use-powershell"></a>使用 PowerShell
作为云操作员，你可以使用 PowerShell **get-azsstorageshare** cmdlet 监视共享的存储容量。 Get-azsstorageshare cmdlet 返回每个共享上的总、已分配和可用空间（以字节为单位）。

![示例：返回共享的可用空间](media/azure-stack-manage-storage-shares/free-space.png)

- **Total 容量**是共享上可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
- "已**用容量**" 是存储租户数据和相关元数据的文件中所有盘区使用的数据量（以字节为单位）。

### <a name="use-the-administrator-portal"></a>使用管理员门户
作为云操作员，你可以使用管理员门户查看所有共享的存储容量。

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。
2. 选择 "**所有服务**" > **存储** > **文件共享**"以打开文件共享列表，你可以在其中查看使用情况信息。

    ![示例： Azure Stack 中心管理员门户中的存储文件共享](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**是共享上可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
   - **使用**的是存储租户数据和相关元数据的文件中所有盘区使用的数据量（以字节为单位）。

### <a name="storage-space-alerts"></a>存储空间警报
使用管理员门户时，会收到有关空间不足的共享的警报。

> [!IMPORTANT]
> 作为云操作员，请保持共享的全部使用量。 如果某个共享使用了100%，则存储服务不再可用于该共享。 若要在利用率为100% 的共享上恢复可用空间和还原操作，你必须联系 Microsoft 支持部门。

**警告**：当文件共享利用率超过80% 时，管理员门户中会出现*警告*警报：

![示例： Azure Stack 中心管理员门户中的警告性警报](media/azure-stack-manage-storage-shares/alert-warning.png)

**严重**：当文件共享利用率超过90% 时，管理员门户中会出现一个*严重*警报：

![示例： Azure Stack 中心管理员门户中的严重警报](media/azure-stack-manage-storage-shares/alert-critical.png)

**查看详细信息**：在管理员门户中，可以打开警报的详细信息以查看缓解选项： ![示例：在 Azure Stack 中心管理员门户中查看警报详细信息](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>管理可用空间
当需要释放共享空间时，请首先使用最不受侵害的方法。 例如，尝试回收空间，然后选择迁移容器。  

### <a name="reclaim-capacity"></a>回收容量
*此选项适用于多节点部署和 Azure Stack 开发工具包。*

你可以回收已删除的租户帐户使用的容量。 当达到数据[保持期](azure-stack-manage-storage-accounts.md#set-the-retention-period)时，会自动回收此容量，也可以立即执行此操作。

有关详细信息，请参阅管理存储资源中的[回收容量](azure-stack-manage-storage-accounts.md#reclaim)。

### <a name="migrate-a-container-between-volumes"></a>在卷之间迁移容器
*此选项仅适用于 Azure Stack 集线器集成系统。*

由于租户使用模式，某些租户共享使用的空间比其他租户要多。 结果可以是在相对未使用的其他共享之前，在空间不足的情况下运行的共享。

可以通过将某些 blob 容器手动迁移到其他共享来释放过度使用的共享上的空间。 可以将几个较小的容器迁移到单个共享上，使其拥有全部功能。 使用迁移来移动*可用*容器。 可用容器是不包含 VM 磁盘的容器。

迁移会在新的共享上合并容器的所有 blob。

- 如果容器已进入溢出模式并已将 blob 放置在其他卷上，则新共享必须具有足够的容量来容纳迁移的容器的所有 blob。 这包括位于其他共享上的 blob。

- PowerShell cmdlet *AzsStorageContainer*仅识别容器的初始卷上使用的空间。 Cmdlet 不会标识放在其他卷上的 blob 所使用的空间。 因此，容器的完整大小可能不明显。 新共享上的容器合并可能会将该新共享发送到溢出状态，在这种情况下，会将数据放置到其他共享上。 因此，你可能需要再次重新平衡共享。

- 如果你没有对资源组的权限，并且不能使用 PowerShell 来查询其他卷的溢出数据，请与这些资源组和容器的所有者合作，以了解迁移数据之前要迁移的数据的总大小。  

> [!IMPORTANT]
> 容器的 blob 迁移是需要使用 PowerShell 的脱机操作。 在迁移完成之前，要迁移的容器的所有 blob 都保持脱机状态，并且不能使用。 还应避免升级 Azure Stack 中心，直到所有正在进行的迁移完成。

#### <a name="to-migrate-containers-using-powershell"></a>使用 PowerShell 迁移容器
1. 确认已[安装并配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](https://go.microsoft.com/fwlink/?LinkId=394767)。
2. 检查容器以了解计划迁移的共享上的数据。 若要确定卷中迁移的最佳候选容器，请使用**AzsStorageContainer** cmdlet：

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   然后检查 $containers：

   ```powershell
   $containers
   ```

   ![示例： $Containers](media/azure-stack-manage-storage-shares/containers.png)

3. 确定用于保存迁移容器的最佳目标共享：

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   然后检查 $destinationshares：

   ```powershell 
   $destinationshares
   ```

   ![示例： $destination 共享](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. 开始迁移容器。 迁移是异步的。 如果在第一次迁移完成之前开始迁移其他容器，请使用作业 ID 跟踪每个容器的状态。

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   然后检查 $jobId。 在以下示例中，请将*d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0*替换为要检查的作业 ID：

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. 使用作业 ID 检查迁移作业的状态。 容器迁移完成后， **MigrationStatus**设置为**完成**。

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![示例：迁移状态](media/azure-stack-manage-storage-shares/migration-status1.png)

6. 你可以取消正在进行的迁移作业。 已取消异步处理已取消的迁移作业。 您可以使用 $jobid 跟踪取消：

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![示例：回滚状态](media/azure-stack-manage-storage-shares/rollback.png)

7. 你可以再次运行步骤6中的命令，直到状态确认迁移作业已**取消**：  

    ![示例：已取消状态](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>移动 VM 磁盘
*此选项仅适用于 Azure Stack 集线器集成系统。*

最极端的空间管理方法涉及到迁移 VM 磁盘。 由于移动附加容器（一个包含 VM 磁盘的容器）非常复杂，因此请联系 Microsoft 支持部门来完成此操作。

## <a name="next-steps"></a>后续步骤
详细了解[如何向用户提供 vm](azure-stack-tutorial-tenant-vm.md)。
