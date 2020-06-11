---
title: 管理 Azure Stack Hub 中的存储容量
description: 了解如何在 Azure Stack Hub 中监视和管理存储容量与可用性。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: ecac1c8c69a8f332a85bf0a934f688f14dbcaddd
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631003"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>管理 Azure Stack Hub 的存储容量

本文帮助 Azure Stack Hub 云操作员监视并管理其 Azure Stack Hub 部署的存储容量。 Azure Stack 中心存储基础结构将作为存储服务的 Azure Stack 中心部署的总存储容量的一个子集。 存储服务将租户的数据存储在卷上对应于部署节点的共享中。

云操作员可以使用的存储量有限。 实施的解决方案定义了存储量。 使用多节点解决方案时，解决方案由 OEM 供应商提供，或由安装 Azure Stack 开发工具包 (ASDK) 的硬件提供。

由于 Azure Stack Hub 不支持存储容量扩展，因此必须[监视](#monitor-shares)可用存储，确保保持操作的效率。

如果卷的剩余可用容量有限，请计划[管理可用空间](#manage-available-space)，以防共享空间不足。

用于管理容量的选项包括：
- 回收容量。
- 正在迁移存储对象。

当对象存储卷使用了100% 时，存储服务将不再能用于该卷。 若要获取恢复卷操作的帮助，请联系 Microsoft 支持部门。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>了解卷、共享、容器和磁盘
### <a name="volumes-and-shares"></a>卷和共享
存储服务将可用的存储分区成相等的独立卷，这些卷分配用于保存租户数据。  有关 Azure Stack 中心中的卷的详细信息，请参阅[管理 Azure Stack 中心的存储基础结构](azure-stack-manage-storage-infrastructure.md)。

对象存储区包含租户数据。 租户数据包括页 Blob、块 Blob、追加 Blob、表、队列、数据库和相关的元数据存储。 对象存储卷的数目等于 Azure Stack 中心部署中的节点数：

- 在四节点部署中，有四个对象存储卷。 在多节点部署中，如果某个节点被删除或出现故障，则卷数量不会减少。
- 如果使用 ASDK，则有一个包含单个共享的卷。

对象存储卷用于独占使用存储服务。 不得直接修改、添加或删除卷上的任何文件。 只能由存储服务处理这些卷中存储的文件。

由于存储对象（blob 等）单独包含在单个卷中，因此每个对象的最大大小不能超过卷的大小。 新的对象的最大大小取决于在创建新对象时卷中保留为未使用空间的容量。

如果对象存储区的可用空间不足，并且[回收](#reclaim-capacity)空间的操作未成功或不可用，Azure Stack 集线器云操作员可以将存储对象从一个卷迁移到另一个卷。

有关租户用户如何处理 Azure Stack 中心内的 blob 存储的信息，请参阅[Azure Stack 中心存储服务](/azure-stack/user/azure-stack-storage-overview)。

### <a name="containers"></a>容器
租户用户可以创建容器用于存储 Blob 数据。 尽管用户决定放置 blob 的容器，但存储服务使用算法来确定放置容器的卷。 此算法通常选择具有最多可用空间的卷。  

将 blob 放置在容器中后，blob 可能会增长，以使用更多空间。 当你添加新的 blob，并且现有的 blob 增长时，保存容器的卷中的可用空间将会收缩。 

容器并不局限于单个卷。 当容器中合并的 Blob 数据增长为使用 80% 或更多可用空间时，容器将进入溢出模式。** 在溢出模式下，在该容器中创建的任何新 Blob 将分配到具有足够空间的其他卷。 一段时间后，处于溢出模式的容器可将 Blob 分布到多个卷。

80% （再后90%）使用卷中的可用空间时，系统会在 Azure Stack 中心管理员门户中引发[警报](#storage-space-alerts)。 云操作员应查看可用的存储容量，并规划重新均衡内容。 到达磁盘的 100% 容量时，存储服务将停止运行，但不会引发其他警报。

### <a name="disks"></a>磁盘
Azure Stack 集线器支持在虚拟机中使用托管磁盘和非托管磁盘，同时作为操作系统（OS）和数据磁盘。

**托管磁盘**通过管理与 VM 磁盘关联的存储帐户简化了 Azure IaaS VM 的磁盘管理。 只需指定所需的磁盘大小，Azure Stack Hub 即可为你创建和管理磁盘。 有关详细信息，请参阅[托管磁盘概述](/azure/virtual-machines/windows/managed-disks-overview)。

建议对 VM 使用托管磁盘，以便更轻松地进行管理和容量平衡。 不需在使用托管磁盘之前准备存储帐户和容器。 创建多个托管磁盘时，会将这些磁盘分配到多个卷中，这有助于平衡卷的容量。  

**非托管磁盘**是指以页 blob 形式存储在 Azure 存储帐户中的 VHD 文件。 租户创建的页 blob 称为 VM 磁盘，存储在存储帐户的容器中。 建议仅将非托管磁盘用于需要与第三方工具兼容的 Vm 才支持 Azure 非托管磁盘。

租户最好是将每个磁盘放入不同的容器，以改善 VM 性能。

- 每个保存虚拟机的磁盘或页 blob 的容器都被视为拥有该磁盘的 VM 的附加容器。
- 不保存 VM 中任何磁盘的容器被视为可用容器。

用于在附加容器中释放空间的选项有限制。 若要了解详细信息，请参阅[分发非托管磁盘](#distribute-unmanaged-disks)。

>[!TIP]  
> 云操作员不会直接操作连接到租户可能添加到容器的 Vm 的非托管磁盘。 但是，当你计划管理存储共享上的空间时，了解非托管磁盘与容器和共享的关系会很有用。

::: moniker range="<azs-2002"
## <a name="monitor-shares"></a>监视共享
使用 Azure PowerShell 或管理员门户监视共享，以便了解可用空间的限制。 使用门户时，会收到有关共享空间不足的警报。

### <a name="use-powershell"></a>使用 PowerShell
作为云操作员，你可以使用 PowerShell cmdlet 监视共享的存储容量 `Get-AzsStorageShare` 。 Cmdlet 将返回每个共享上的总、已分配和可用空间（以字节为单位）。

![示例：返回共享的可用空间](media/azure-stack-manage-storage-shares/free-space.png)

- **总容量**：共享上可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
- 已**用容量**：存储租户数据和相关元数据的文件中所有区使用的数据量（以字节为单位）。

### <a name="use-the-administrator-portal"></a>使用管理员门户
云操作员可以使用管理员门户来查看所有共享的存储容量。

1. 登录到管理员门户 `https://adminportal.local.azurestack.external` 。
2. 选择 "**所有服务**" > "**存储** > **文件共享**" 以打开 "文件共享" 列表，您可以在其中查看使用情况信息。

    ![示例： Azure Stack 中心管理员门户中的存储文件共享](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**：共享上可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
   - 已**使用**：存储租户数据和相关元数据的文件中所有区使用的数据量（以字节为单位）。

::: moniker-end
::: moniker range=">=azs-2002"

## <a name="monitor-volumes"></a>监视卷
使用 PowerShell 或管理员门户监视卷，以便了解可用空间的限制。 使用门户时，会收到有关磁盘空间不足的警报。

### <a name="use-powershell"></a>使用 PowerShell
作为云操作员，你可以使用 PowerShell cmdlet 监视卷的存储容量 `Get-AzsVolume` 。 Cmdlet 将返回每个卷上的总容量和可用空间（GB）。

![示例：返回卷的可用空间](media/azure-stack-manage-storage-shares/listvolumespowershell.png)

- **总容量**：共享上可用的总空间（GB）。 此空间用于存储服务维护的数据和元数据。
- **剩余容量**：存储租户数据和相关元数据的可用空间量（GB）。

### <a name="use-the-administrator-portal"></a>使用管理员门户
作为云操作员，你可以使用管理员门户查看所有卷的存储容量。

1. 登录到 Azure Stack 中心管理员门户（ `https://adminportal.local.azurestack.external` ）。
2. 选择 "**所有服务**" " > **存储** > **卷**" 以打开卷列表，你可以在其中查看使用情况信息。

    ![示例： Azure Stack 中心管理员门户中的存储卷](media/azure-stack-manage-storage-shares/listvolumes.png)

   - **Total**：卷上可用的总空间。 此空间用于存储服务维护的数据和元数据。
   - 已**使用**：存储租户数据和相关元数据的文件中的所有区所使用的数据量。

::: moniker-end

### <a name="storage-space-alerts"></a>存储空间警报
使用管理员门户时，会收到有关空间不足的卷的警报。

> [!IMPORTANT]
> 作为云操作员，你应防止共享达到完全使用。 当共享利用率达到 100% 时，不再能够针对该共享运行存储服务。 若要在利用率为100% 的共享上恢复可用空间和还原操作，必须联系 Microsoft 支持部门。

* **警告**：当文件共享利用率超过80% 时，管理员门户中会出现*警告*警报：

  ![示例： Azure Stack 中心管理员门户中的警告性警报](media/azure-stack-manage-storage-shares/alert-warning.png)

* **严重**：当文件共享利用率超过90% 时，管理员门户中会出现一个*严重*警报：

  ![示例： Azure Stack 中心管理员门户中的严重警报](media/azure-stack-manage-storage-shares/alert-critical.png)

* **查看详细信息**：在管理员门户中，可以打开警报的详细信息以查看缓解选项：

  ![示例：在 Azure Stack 中心管理员门户中查看警报详细信息](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>管理可用空间
当需要释放卷上的空间时，请首先使用最不受侵害的方法。 例如，尝试在选择迁移托管磁盘之前收回空间。  

### <a name="reclaim-capacity"></a>回收容量

你可以回收已删除的租户帐户使用的容量。 当数据达到[保留期](azure-stack-manage-storage-accounts.md#set-the-retention-period)时，系统会自动回收此容量，或者，你也可以立即回收此容量。

有关详细信息，请参阅[管理 Azure Stack 中心存储帐户](azure-stack-manage-storage-accounts.md#reclaim)的 "回收容量" 一节。

::: moniker range="<azs-1910"

### <a name="migrate-a-container-between-volumes"></a>在卷之间迁移容器
*此选项仅适用于 Azure Stack 集线器集成系统。*

由于租户使用模式方面的原因，某些租户共享使用的空间比其他共享要多。 这可能会导致某些共享的空间在相对未使用的其他共享之前运行不足。

可将某些 Blob 容器手动迁移到不同的共享，在过度使用的共享中释放空间。 可将多个较小容器迁移到单个共享，前提是该共享可以提供足够的容量来保存所有这些容器。 使用迁移操作来移动可用容器。** 可用容器是不包含 VM 磁盘的容器。

迁移过程会在新的共享中合并容器的所有 Blob。

- 如果容器进入溢出模式且 Blob 已放置在其他卷上，则新共享必须提供足够的容量来保存迁移的容器的所有 Blob。 这包括位于其他共享中的 Blob。

- PowerShell cmdlet `Get-AzsStorageContainer` 仅识别容器的初始卷上使用的空间。 Cmdlet 不会标识放在其他卷上的 blob 所使用的空间。 因此，容器的完整大小可能不明显。 新共享上的容器合并可能会将该新共享发送到溢出状态，在这种情况下，会将数据放置到其他共享上。 因此，你可能需要重新平衡共享。

- 如果你没有对某些资源组的权限，并且不能使用 PowerShell 来查询其他卷的溢出数据，请与这些资源组和容器的所有者合作，以了解在迁移之前要迁移的总数据量。  

> [!IMPORTANT]
> 容器的 blob 迁移是需要使用 PowerShell 的脱机操作。 在迁移完成之前，要迁移的容器的所有 blob 都保持脱机状态，并且不能使用。 还应避免升级 Azure Stack 中心，直到所有正在进行的迁移完成。

#### <a name="migrate-containers-by-using-powershell"></a>使用 PowerShell 迁移容器
1. 确认已[安装并配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关详细信息，请参阅[使用 Azure PowerShell 管理 Azure 资源](https://go.microsoft.com/fwlink/?LinkId=394767)。
2. 检查容器，了解要迁移的共享中包含哪种数据。 若要确定卷中迁移的最佳候选容器，请使用 `Get-AzsStorageContainer` cmdlet：

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   然后检查 $containers：

   ```powershell
   $containers
   ```

   ![示例： $containers](media/azure-stack-manage-storage-shares/containers.png)

3. 确定用于保存要迁移的容器的最佳目标共享：

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   然后检查 $destinationshares：

   ```powershell 
   $destinationshares
   ```

   ![示例：$destination shares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. 开始迁移容器。 迁移是异步操作。 如果在第一次迁移完成之前开始迁移其他容器，请使用作业 ID 跟踪每个容器的状态。

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   然后检查 $jobId。 在以下示例中，请将 *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* 替换为要检查的作业 ID：

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. 使用作业 ID 检查迁移作业的状态。 容器迁移完成后，**MigrationStatus** 会设置为 *Complete*。

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![示例：迁移状态](media/azure-stack-manage-storage-shares/migration-status1.png)

6. 可以取消正在进行的迁移作业。 系统会以异步方式处理已取消的迁移作业。 您可以使用 $jobid 跟踪取消：

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![示例：回滚状态](media/azure-stack-manage-storage-shares/rollback.png)

7. 你可以再次运行步骤6中的命令，直到迁移状态为 "已*取消*"：  

    ![示例：“已取消”状态](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>移动 VM 磁盘
*此选项仅适用于 Azure Stack 集线器集成系统。*

管理空间的最极端方法涉及移动 VM 磁盘。 由于移动附加容器（一个包含 VM 磁盘的容器）非常复杂，因此请联系 Microsoft 支持部门来完成此操作。

::: moniker-end
::: moniker range=">=azs-1910"

### <a name="migrate-a-managed-disk-between-volumes"></a>在卷之间迁移托管磁盘
*此选项仅适用于 Azure Stack 集线器集成系统。*

由于租户使用模式，某些租户卷使用的空间比其他租户要多。 结果可能是在另一个相对不使用的卷之前，空间上运行空间不足的卷。

你可以通过将某些托管磁盘手动迁移到其他卷来释放使用过度的卷上的空间。 你可以将多个托管磁盘迁移到一个卷，使其拥有全部存储空间。 使用迁移来移动*脱机*托管磁盘。 脱机托管磁盘是指未连接到 VM 的磁盘。

> [!IMPORTANT]
> 迁移托管磁盘是一种脱机操作，需要使用 PowerShell。 在启动迁移作业之前，必须将候选磁盘从其所有者 VM 中分离出来（迁移作业完成后，可以重新附加这些磁盘）。 在迁移完成之前，要迁移的所有托管磁盘都必须保持脱机状态且不能使用，否则迁移作业将中止，并且所有已取消迁移磁盘仍保留在其原始卷上。 还应避免升级 Azure Stack 中心，直到所有正在进行的迁移完成。

#### <a name="to-migrate-managed-disks-using-powershell"></a>使用 PowerShell 迁移托管磁盘
1. 确认已安装并配置 Azure PowerShell。 有关配置 PowerShell 环境的说明，请参阅为[Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。 若要登录 Azure Stack 中心，请参阅[配置操作员环境和登录到 Azure Stack 中心](azure-stack-powershell-configure-admin.md)。
2. 检查托管磁盘以了解你计划迁移的卷上的磁盘。 若要确定卷中迁移的最佳候选磁盘，请使用 `Get-AzsDisk` cmdlet：

   ```powershell  
   $ScaleUnit = (Get-AzsScaleUnit)[0]
   $StorageSubSystem = (Get-AzsStorageSubSystem -ScaleUnit $ScaleUnit.Name)[0]
   $Volumes = (Get-AzsVolume -ScaleUnit $ScaleUnit.Name -StorageSubSystem $StorageSubSystem.Name | Where-Object {$_.VolumeLabel -Like "ObjStore_*"})
   $SourceVolume  = ($Volumes | Sort-Object RemainingCapacityGB)[0]
   $VolumeName = $SourceVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationSource = "\\SU1FileServer."+$VolumeName+"\SU1_"+$SourceVolume.VolumeLabel
   $Disks = Get-AzsDisk -Status All -SharePath $MigrationSource | Select-Object -First 10
   ```
   然后检查 $disks：

   ```powershell
   $Disks
   ```

   ![示例： $Disks](media/azure-stack-manage-storage-shares/disks.png)

3. 确定用于存放所迁移磁盘的最佳目标卷：

   ```powershell
   $DestinationVolume  = ($Volumes | Sort-Object RemainingCapacityGB -Descending)[0]
   $VolumeName = $DestinationVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationTarget = "\\SU1FileServer."+$VolumeName+"\SU1_"+$DestinationVolume.VolumeLabel
   ```

4. 开始迁移托管磁盘。 迁移是异步操作。 如果在第一次迁移完成之前开始迁移其他磁盘，请使用作业名称跟踪每个磁盘的状态。

   ```powershell
   $jobName = "MigratingDisk"
   Start-AzsDiskMigrationJob -Disks $Disks -TargetShare $MigrationTarget -Name $jobName
   ```

5. 使用作业名称来检查迁移作业的状态。 磁盘迁移完成后， **MigrationStatus**设置为**完成**。

   ```powershell 
   $job = Get-AzsDiskMigrationJob -Name $jobName
   ```

   ![示例：迁移状态](media/azure-stack-manage-storage-shares/diskmigrationjob.png)
   
   如果要在一个迁移作业中迁移多个托管磁盘，还可以检查作业的子任务。

   ```powershell 
   $job.Subtasks
   ```

   ![示例：迁移子任务状态](media/azure-stack-manage-storage-shares/diskmigrationsubtask.png)

6. 可以取消正在进行的迁移作业。 系统会以异步方式处理已取消的迁移作业。 您可以使用作业名称跟踪取消操作，直至状态确认迁移作业已**取消**：

   ```powershell
   Stop-AzsDiskMigrationJob -Name $jobName
   ```

   ![示例：“已取消”状态](media/azure-stack-manage-storage-shares/diskmigrationstop.png)

### <a name="distribute-unmanaged-disks"></a>分发非托管磁盘
*此选项仅适用于 Azure Stack 集线器集成系统。*

用于管理空间的最极端方法涉及移动非托管磁盘。 如果租户将数量的非托管磁盘添加到一个容器，则容器的总已用容量可能会超出容器进入*溢出*模式之前保留的容量。 为了避免单个容器用尽卷空间，租户可以将一个容器的现有非托管磁盘分发到不同的容器。 由于分发附加容器（一个包含 VM 磁盘的容器）非常复杂，因此请联系 Microsoft 支持部门来完成此操作。

::: moniker-end

## <a name="next-steps"></a>后续步骤
若要详细了解如何向用户提供 Vm，请参阅[管理 Azure Stack 集线器的存储容量](azure-stack-tutorial-tenant-vm.md)。
