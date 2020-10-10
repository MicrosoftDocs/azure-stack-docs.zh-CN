---
title: 管理 Azure Stack Hub 中的存储容量
description: 了解如何在 Azure Stack Hub 中监视和管理存储容量与可用性。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 21a8d4f5238af436474cb33a41e6e35fbab3afb7
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899731"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>管理 Azure Stack Hub 的存储容量

本文帮助 Azure Stack Hub 云操作员监视并管理其 Azure Stack Hub 部署的存储容量。 Azure Stack Hub 存储基础结构分配 Azure Stack Hub 部署的一部分总存储容量作为存储服务。 存储服务将租户的数据存储在卷上对应于部署节点的共享中。

云操作员可以使用的存储量有限。 实施的解决方案定义了存储量。 使用多节点解决方案时，解决方案由 OEM 供应商提供，或由安装 Azure Stack 开发工具包 (ASDK) 的硬件提供。

Azure Stack 集线器仅支持通过添加更多扩展单元节点来扩展存储容量。 有关详细信息，请参阅 [在 Azure Stack Hub 中添加更多扩展单元节点](azure-stack-add-scale-node.md)。 将物理磁盘添加到节点不会扩展存储容量。

请务必 [监视](#monitor-shares) 可用的存储，以确保保持有效的操作。 当卷的剩余可用容量有限时，请规划[管理可用空间](#manage-available-space)以免共享的容量不足。

用于管理容量的选项包括：


- 回收容量。
- 迁移存储对象。

当对象存储卷利用率达到 100% 时，不再能够针对该卷运行存储服务。 若要获取恢复卷操作的帮助，请联系 Microsoft 支持部门。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>了解卷、共享、容器和磁盘

### <a name="volumes-and-shares"></a>卷和共享

存储服务将可用的存储分区成相等的独立卷，这些卷分配用于保存租户数据。 有关 Azure Stack Hub 中卷的详细信息，请参阅[管理 Azure Stack Hub 的存储基础结构](azure-stack-manage-storage-infrastructure.md)。

对象存储卷保存租户数据。 租户数据包括页 Blob、块 Blob、追加 Blob、表、队列、数据库和相关的元数据存储。 对象存储卷的数目等于 Azure Stack Hub 部署中的节点数目：

- 在包含四个节点的部署中，有四个对象存储卷。 在多节点部署中，如果某个节点被删除或出现故障，卷数目不会减少。
- 如果使用 ASDK，则有一个包含单个共享的卷。

对象存储卷专用于存储服务。 不得直接修改、添加或删除卷上的任何文件。 只能由存储服务处理这些卷中存储的文件。

由于存储对象（Blob 等）各自包含在单个卷中，因此每个对象的大小上限不能超过卷大小。 新对象的大小上限取决于创建新对象时卷中仍未使用的空间容量。

当对象存储卷的可用空间不足且[回收](#reclaim-capacity)空间的操作不成功或不可用时，Azure Stack Hub 云操作人员可以将存储对象从一个共卷迁移到另一个卷。

有关租户用户如何使用 Azure Stack Hub 中的 Blob 存储的详细信息，请参阅 [Azure Stack Hub 存储服务](../user/azure-stack-storage-overview.md)。

### <a name="containers"></a>容器

租户用户可以创建容器用于存储 Blob 数据。 用户可以确定要在哪个容器中放置 Blob，而存储服务使用算法来确定要在哪个卷中放置容器。 此算法通常选择具有最多可用空间的卷。  

将 Blob 置于容器中后，该 Blob 可以增长以使用更多空间。 随着新 Blob 的添加和现有 Blob 的增长，卷中保存该容器的可用空间会不断缩减。 

容器不限于单个卷。 当容器中合并的 Blob 数据增长为使用 80% 或更多可用空间时，容器将进入溢出模式。 在溢出模式下，在该容器中创建的任何新 Blob 将分配到具有足够空间的其他卷。 一段时间后，处于溢出模式的容器可将 Blob 分布到多个卷。

达到卷中 80% 到 90% 的可用空间时，系统会在 Azure Stack Hub 管理员门户中引发[警报](#storage-space-alerts)。 云操作员应查看可用的存储容量，并规划重新均衡内容。 到达磁盘的 100% 容量时，存储服务将停止运行，但不会引发其他警报。

### <a name="disks"></a>磁盘

Azure Stack Hub 支持在 VM 上使用托管磁盘和非托管磁盘，作为操作系统 (OS) 磁盘和数据磁盘。

**托管磁盘**通过管理与 VM 磁盘关联的存储帐户简化了 Azure IaaS VM 的磁盘管理。 只需指定所需的磁盘大小，Azure Stack Hub 即可为你创建和管理磁盘。 有关详细信息，请参阅[托管磁盘概述](/azure/virtual-machines/windows/managed-disks-overview)。

建议对 VM 使用托管磁盘，以便更轻松地进行管理和容量平衡。 不需在使用托管磁盘之前准备存储帐户和容器。 创建多个托管磁盘时，会将这些磁盘分配到多个卷中，这有助于平衡卷的容量。  

非托管磁盘是指以页 blob 形式存储在 Azure 存储帐户中的 VHD 文件。 租户创建的页 blob 称为 VM 磁盘并且存储在存储帐户中的容器中。 建议仅将非托管磁盘用于需要与第三方工具兼容且仅支持 Azure 非托管磁盘的 VM。

租户最好是将每个磁盘放入不同的容器，以改善 VM 性能。

- VM 中保存磁盘的每个容器（或页 Blob）被视为拥有此磁盘的 VM 上的附加容器。
- 不保存 VM 中任何磁盘的容器被视为可用容器。

用于在附加容器中释放空间的选项有限制。 若要了解详细信息，请参阅 [分布非托管磁盘](#distribute-unmanaged-disks)。

>[!TIP]  
> 云操作员不会直接操作已附加到可能由租户添加到容器的 VM 上的非托管磁盘。 但是，在规划如何管理存储共享上的空间时，最好是了解非托管磁盘与容器和共享之间的关系。

::: moniker range="<azs-2002"
## <a name="monitor-shares"></a>监视共享

使用 Azure PowerShell 或管理员门户来监视共享，以便了解可用空间何时受限。 使用门户时，会收到有关共享空间不足的警报。

### <a name="use-powershell"></a>使用 PowerShell

云操作员可以使用 PowerShell `Get-AzsStorageShare` cmdlet 来监视共享的存储容量。 该 cmdlet 返回每个共享中总计、已分配和可用的空间（以字节为单位）。

![示例：返回共享的可用空间](media/azure-stack-manage-storage-shares/free-space.png)

- **总容量**：共享中可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
- **已用容量**：存储租户数据和相关元数据的文件中所有盘区使用的数据量（以字节为单位）。

### <a name="use-the-administrator-portal"></a>使用管理员门户

云操作员可以使用管理员门户来查看所有共享的存储容量。

1. 登录到管理员门户 `https://adminportal.local.azurestack.external`。
2. 选择“所有服务”>“存储”>“文件共享”打开文件共享列表，可以在其中查看使用情况信息  。

    ![示例：Azure Stack Hub 管理员门户中的存储文件共享](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**：共享中可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
   - **已用**：存储租户数据和相关元数据的文件中所有盘区使用的数据量（以字节为单位）。

::: moniker-end
::: moniker range=">=azs-2002"

## <a name="monitor-volumes"></a>监视卷

使用 PowerShell 或管理员门户来监视卷，以便了解可用空间何时受限。 使用门户时，会收到有关卷空间不足的警报。

### <a name="use-powershell"></a>使用 PowerShell

云操作员可以使用 PowerShell `Get-AzsVolume` cmdlet 来监视卷的存储容量。 该 cmdlet 返回每个卷中总计和可用的空间 (GB)。

![示例：返回卷的可用空间](media/azure-stack-manage-storage-shares/listvolumespowershell.png)

- **总容量**：共享中可用的总空间 (GB)。 此空间用于存储服务维护的数据和元数据。
- **剩余容量**：可用于存储租户数据和相关元数据的空间量 (GB)。

### <a name="use-the-administrator-portal"></a>使用管理员门户

云操作员可以使用管理员门户来查看所有卷的存储容量。

1. 登录到 Azure Stack Hub 管理员门户 (`https://adminportal.local.azurestack.external`)。
2. 选择“所有服务”>“存储”>“卷”打开卷列表，可以在其中查看使用情况信息  。

    ![示例：Azure Stack Hub 管理员门户中的存储卷](media/azure-stack-manage-storage-shares/listvolumes.png)

   - **Total**：卷上可用的空间总量。 此空间用于存储服务维护的数据和元数据。
   - **已用**：存储租户数据和相关元数据的文件中所有盘区使用的数据量。

::: moniker-end

### <a name="storage-space-alerts"></a>存储空间警报

使用管理员门户时，会收到有关卷空间不足的警报。

> [!IMPORTANT]
> 云操作员应该避免共享达到用完状态。 当共享利用率达到 100% 时，不再能够针对该共享运行存储服务。 若要在利用率为100% 的共享上恢复可用空间和还原操作，必须联系 Microsoft 支持部门。

* **警告**：当文件共享利用率超过 80% 时，管理员门户中会显示“警告”警报：

  ![示例：Azure Stack Hub 管理员门户中的警告性警报](media/azure-stack-manage-storage-shares/alert-warning.png)

* **严重**：当文件共享利用率超过 90% 时，管理员门户中会显示“严重”警报：

  ![示例：Azure Stack Hub 管理员门户中的严重警报](media/azure-stack-manage-storage-shares/alert-critical.png)

* **查看详细信息**：在管理员门户中，可以打开警报的详细信息来查看缓解选项：

  ![示例：在 Azure Stack Hub 管理员门户中查看警报详细信息](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>管理可用空间

需要释放卷中的可用空间时，请先使用破坏性最低的方法。 例如，先尝试回收空间，然后再选择迁移托管磁盘。  

### <a name="reclaim-capacity"></a>回收容量

可以回收已删除的租户帐户使用的容量。 当数据达到[保留期](azure-stack-manage-storage-accounts.md#set-the-retention-period)时，系统会自动回收此容量，或者，你也可以立即回收此容量。

有关详细信息，请参阅[管理 Azure Stack Hub 存储帐户](azure-stack-manage-storage-accounts.md#reclaim)的“回收容量”部分。

::: moniker range="<azs-1910"

### <a name="migrate-a-container-between-volumes"></a>在卷之间迁移容器

此选项仅适用于 Azure Stack Hub 集成系统。

由于租户使用模式方面的原因，某些租户共享使用的空间比其他共享要多。 这可能会导致某些共享在其他相对用得极少的共享之前就遇到空间不足的情况。

可将某些 Blob 容器手动迁移到不同的共享，在过度使用的共享中释放空间。 可将多个较小容器迁移到单个共享，前提是该共享可以提供足够的容量来保存所有这些容器。 使用迁移操作来移动可用容器。 可用容器是不包含 VM 磁盘的容器。

迁移过程会在新的共享中合并容器的所有 Blob。

- 如果容器进入溢出模式且 Blob 已放置在其他卷上，则新共享必须提供足够的容量来保存迁移的容器的所有 Blob。 这包括位于其他共享中的 Blob。

- PowerShell cmdlet `Get-AzsStorageContainer` 只识别容器的初始卷上的已用空间。 此 cmdlet 不识别已置于其他卷上的 Blob 使用的空间。 因此，容器的完整大小可能不明显。 新共享中的容器整合可能会使该共享进入溢出状态，以便将数据置于其他共享。 因此，可能需要重新均衡共享。

- 如果你缺少对特定资源组的权限，且无法使用 PowerShell 来查询溢出数据的其他卷，请配合这些资源组和容器的所有者，在迁移数据之前了解要迁移的总数据量。  

> [!IMPORTANT]
> 迁移容器的 Blob 是一项脱机操作，需要用到 PowerShell。 在迁移完成之前，要迁移的容器的所有 Blob 会保持脱机状态且不可使用。 还应该避免在所有现有迁移操作完成之前升级 Azure Stack Hub。

#### <a name="migrate-containers-by-using-powershell"></a>使用 PowerShell 迁移容器

1. 确认已[安装并配置 Azure PowerShell](/powershell/azure/)。 有关详细信息，请参阅[使用 Azure PowerShell 管理 Azure 资源](https://go.microsoft.com/fwlink/?LinkId=394767)。
2. 检查容器，了解要迁移的共享中包含哪种数据。 若要识别卷中可迁移的最佳候选容器，请使用 `Get-AzsStorageContainer` cmdlet：

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   然后检查 $containers：

   ```powershell
   $containers
   ```

   ![示例：$containers](media/azure-stack-manage-storage-shares/containers.png)

3. 识别用于保存要迁移的容器的最佳目标共享：

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   然后检查 $destinationshares：

   ```powershell 
   $destinationshares
   ```

   ![示例：$destination shares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. 开始迁移容器。 迁移是异步操作。 如果在首次迁移完成之前开始迁移其他容器，请使用作业 ID 来跟踪每个容器的状态。

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

   ![显示迁移状态的屏幕截图。](media/azure-stack-manage-storage-shares/migration-status1.png)

6. 可以取消正在进行的迁移作业。 系统会以异步方式处理已取消的迁移作业。 可以使用 $jobid 跟踪取消操作：

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![示例：回退状态](media/azure-stack-manage-storage-shares/rollback.png)

7. 可以再次运行步骤 6 中的命令，直到迁移状态为 *Canceled*：  

    ![屏幕截图，显示已取消迁移状态的示例。](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>移动 VM 磁盘

此选项仅适用于 Azure Stack Hub 集成系统。

用于管理空间的最极端方法涉及到移动 VM 磁盘。 由于将附加容器 (包含 VM 磁盘的容器) 很复杂，因此请联系 Microsoft 支持部门来完成此操作。

::: moniker-end
::: moniker range=">=azs-1910"

### <a name="migrate-a-managed-disk-between-volumes"></a>在卷间迁移托管磁盘

此选项仅适用于 Azure Stack Hub 集成系统。

由于租户使用模式方面的原因，某些租户卷使用的空间比其他卷要多。 结果可能是，某个卷在其他相对用得极少的卷之前就遇到了空间不足的情况。

可将某些托管磁盘手动迁移到其他卷，以释放过度使用的卷上的空间。 可将多个托管磁盘迁移到有足够的容量来保存它们的单个卷。 使用迁移移动脱机托管磁盘。 脱机托管磁盘是指未附加到 VM 的磁盘。

> [!IMPORTANT]
> 迁移托管磁盘是一项脱机操作，需要用到 PowerShell。 在开始迁移作业之前，必须将要迁移的候选磁盘从其所有者 VM 中拆离出来（迁移作业完成后，可以重新附加这些磁盘）。 在迁移完成之前，要迁移的所有托管磁盘都必须保持脱机状态且不能使用，否则迁移作业将中止，并且所有已取消迁移的磁盘仍保留在其原始卷上。 还应该避免在所有现有迁移操作完成之前升级 Azure Stack Hub。

#### <a name="to-migrate-managed-disks-using-powershell"></a>使用 PowerShell 迁移托管磁盘

1. 确认已安装并配置 Azure PowerShell。 有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack Hub，请参阅[配置操作员环境并登录到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)。
2. 检查托管磁盘以了解计划迁移的卷上有哪些磁盘。 若要识别卷中可迁移的最佳候选磁盘，请使用 `Get-AzsDisk` cmdlet：

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

   ![示例：$Disks](media/azure-stack-manage-storage-shares/disks.png)

3. 识别用于保存要迁移的磁盘的最佳目标卷：

   ```powershell
   $DestinationVolume  = ($Volumes | Sort-Object RemainingCapacityGB -Descending)[0]
   $VolumeName = $DestinationVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationTarget = "\\SU1FileServer."+$VolumeName+"\SU1_"+$DestinationVolume.VolumeLabel
   ```

4. 开始迁移托管磁盘。 迁移是异步操作。 如果在首次迁移完成之前开始迁移其他磁盘，请使用作业名称来跟踪每个磁盘的状态。

   ```powershell
   $jobName = "MigratingDisk"
   Start-AzsDiskMigrationJob -Disks $Disks -TargetShare $MigrationTarget -Name $jobName
   ```

5. 使用作业名称检查迁移作业的状态。 磁盘迁移完成后，MigrationStatus 会设置为 Complete 。

   ```powershell 
   $job = Get-AzsDiskMigrationJob -Name $jobName
   ```

   ![示例：迁移状态](media/azure-stack-manage-storage-shares/diskmigrationjob.png)
   
   如果要在一个迁移作业中迁移多个托管磁盘，还可以检查作业的子任务。

   ```powershell 
   $job.Subtasks
   ```

   ![示例：迁移子任务状态](media/azure-stack-manage-storage-shares/diskmigrationsubtask.png)

6. 可以取消正在进行的迁移作业。 系统会以异步方式处理已取消的迁移作业。 可以使用作业名称跟踪取消操作，直至状态确认迁移作业“已取消”：

   ```powershell
   Stop-AzsDiskMigrationJob -Name $jobName
   ```

   ![示例：“已取消”状态](media/azure-stack-manage-storage-shares/diskmigrationstop.png)

### <a name="distribute-unmanaged-disks"></a>分布非托管磁盘

此选项仅适用于 Azure Stack Hub 集成系统。

用于管理空间的最极端方法涉及到移动非托管磁盘。 如果租户将多个非托管磁盘添加到一个容器，则在该容器进入溢出模式之前，该容器的总已用容量可能会超出其所在卷的可用容量。 为了避免单个容器用尽卷空间，租户可以将一个容器的现有非托管磁盘分布到其他容器。 由于将附加容器)  (包含 VM 磁盘的容器，因此请与 Microsoft 支持部门联系以完成此操作。

::: moniker-end

## <a name="next-steps"></a>后续步骤

若要详细了解如何向用户提供 VM，请参阅[管理 Azure Stack Hub 的存储容量](./tutorial-offer-services.md?view=azs-2002)。
