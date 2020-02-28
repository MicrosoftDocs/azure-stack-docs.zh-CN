---
title: 管理 Azure Stack 集线器中的存储容量
description: 了解如何监视和管理 Azure Stack 集线器中的存储容量和可用性。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 212f68be85bfe4b129e84057cc63472e5259a41c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699195"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>管理 Azure Stack 集线器的存储容量

本文有助于 Azure Stack 中心云操作员监视和管理其 Azure Stack 中心部署的存储容量。 Azure Stack 中心存储基础结构将作为*存储服务*的 Azure Stack 中心部署的总存储容量的一个子集。 存储服务将租户的数据存储在与部署的节点相对应的卷上的共享中。

作为云操作员，你可以使用有限数量的存储空间。 存储量由您实现的解决方案定义。 此解决方案是在使用多节点解决方案时由 OEM 供应商提供的，或者是由安装 Azure Stack 开发工具包的硬件（ASDK）提供的。

由于 Azure Stack 集线器不支持存储容量的扩展，因此必须[监视](#monitor-shares)可用的存储，以确保保持有效的操作。

当共享的剩余可用容量有限时，计划[管理可用空间](#manage-available-space)，以防止共享空间不足。

用于管理容量的选项包括：
- 回收容量。
- 迁移容器。

当共享使用率100% 时，存储服务将不再为该共享提供功能。 若要获取有关还原共享操作的帮助，请联系 Microsoft 支持部门。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>了解卷和共享、容器和磁盘
### <a name="volumes-and-shares"></a>卷和共享
*存储服务*将可用存储分区为单独的、相等的卷，这些卷分配用于保存租户数据。 卷数等于 Azure Stack 中心部署中的节点数：

- 在四节点部署中，有四个卷。 每个卷都有一个共享。 在多节点部署中，如果某个节点被删除或出现故障，则共享的数量不会减少。
- 如果你使用 ASDK，则会有一个包含单个共享的卷。

由于存储服务共享用于独占使用存储服务，因此不能直接修改、添加或删除共享上的任何文件。 只有存储服务才能处理这些卷中存储的文件。

卷上的共享保存租户数据。 租户数据包括页 blob、块 blob、追加 blob、表、队列、数据库和相关的元数据存储区。 由于存储对象（blob 等）单独包含在单个共享内，因此每个对象的最大大小不能超过共享大小。 新的对象的最大大小取决于创建新对象时在共享中保留为未使用空间的容量。

如果某个共享空间不足，并且[回收](#reclaim-capacity)空间的操作未成功或不可用，Azure Stack 集线器云操作员可以将 blob 容器从一个共享迁移到另一个共享。

有关租户用户如何处理 Azure Stack 中心内的 blob 存储的信息，请参阅[Azure Stack 中心存储服务](/azure-stack/user/azure-stack-storage-overview)。

### <a name="containers"></a>容器
租户用户创建用于存储 blob 数据的容器。 尽管用户决定放置 blob 的容器，但存储服务使用算法来确定放置容器的卷。 算法通常会选择具有最多可用空间的卷。  

将 blob 放置在容器中后，blob 可能会增长，以使用更多空间。 当你添加新的 blob，并且现有的 blob 增长时，保存容器的卷中的可用空间将会收缩。  

容器并不局限于单个共享。 当容器中的组合 blob 数据增长到使用80% 或更多的可用空间时，容器将进入*溢出*模式。 如果容器处于溢出模式，则在容器中创建的任何新 blob 都将分配给具有足够空间的其他卷。 随着时间的推移，溢出模式下的容器可以具有跨多个卷分布的 blob。

如果使用了卷中的可用空间80% （即90%），则系统会在 Azure Stack 中心管理员门户中引发警报。 云操作员应查看可用的存储容量，并计划重新平衡内容。 当磁盘使用100% 且未引发其他警报时，存储服务将停止工作。

### <a name="disks"></a>磁盘
虚拟机（VM）磁盘由租户添加到容器，并且包括操作系统磁盘。 Vm 还可以有一个或多个数据磁盘。 这两种类型的磁盘都存储为页 blob。 租户的指导是将每个磁盘放置在单独的容器中，以提高 VM 的性能。

- 每个保存虚拟机的磁盘或页 blob 的容器都被视为拥有该磁盘的 VM 的附加容器。
- 不保存 VM 中任何磁盘的容器被视为可用容器。

用于释放附加容器上空间的选项是有限的。 若要了解详细信息，请参阅[移动 VM 磁盘](#move-vm-disks)。

>[!TIP]  
> 云操作员不直接管理附加到租户可能添加到容器的 Vm 的磁盘。 但是，当你计划管理存储共享上的空间时，了解磁盘与容器和共享的关系会很有用。

## <a name="monitor-shares"></a>监视共享
使用 Azure PowerShell 或管理员门户监视共享，以便了解可用空间的限制。 使用门户时，会收到有关空间不足的共享的警报。

### <a name="use-powershell"></a>使用 PowerShell
作为云操作员，你可以使用 PowerShell `Get-AzsStorageShare` cmdlet 来监视共享的存储容量。 Cmdlet 将返回每个共享上的总、已分配和可用空间（以字节为单位）。

![示例：返回共享的可用空间](media/azure-stack-manage-storage-shares/free-space.png)

- **总容量**：共享上可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
- 已**用容量**：存储租户数据和相关元数据的文件中所有区使用的数据量（以字节为单位）。

### <a name="use-the-administrator-portal"></a>使用管理员门户
作为云操作员，你可以使用管理员门户查看所有共享的存储容量。

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。
2. 选择 "**所有服务**" > **存储** > **文件共享**"，以打开" 文件共享 "列表，您可以在其中查看使用情况信息。

    ![示例： Azure Stack 中心管理员门户中的存储文件共享](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**：共享上可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
   - 已**使用**：存储租户数据和相关元数据的文件中所有区使用的数据量（以字节为单位）。

### <a name="storage-space-alerts"></a>存储空间警报
使用管理员门户时，会收到有关空间不足的共享的警报。

> [!IMPORTANT]
> 作为云操作员，你应防止共享达到完全使用。 当共享使用率100% 时，存储服务将不再为该共享提供功能。 若要在使用率为100% 的共享上恢复可用空间和还原操作，必须联系 Microsoft 支持部门。

* **警告**：当文件共享利用率超过80% 时，管理员门户中会出现*警告*警报：

  ![示例： Azure Stack 中心管理员门户中的警告性警报](media/azure-stack-manage-storage-shares/alert-warning.png)

* **严重**：当文件共享利用率超过90% 时，管理员门户中会出现一个*严重*警报：

  ![示例： Azure Stack 中心管理员门户中的严重警报](media/azure-stack-manage-storage-shares/alert-critical.png)

* **查看详细信息**：在管理员门户中，可以打开警报的详细信息以查看缓解选项：

  ![示例：在 Azure Stack 中心管理员门户中查看警报详细信息](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>管理可用空间
如果需要释放共享空间，请首先使用最不受侵害的方法。 例如，尝试回收空间，然后选择迁移容器。  

### <a name="reclaim-capacity"></a>回收容量
*此选项适用于多节点部署和 Azure Stack 开发工具包。*

你可以回收已删除的租户帐户使用的容量。 当达到数据[保持期](azure-stack-manage-storage-accounts.md#set-the-retention-period)时，会自动回收此容量，也可以立即执行此操作。

有关详细信息，请参阅[管理 Azure Stack 中心存储帐户](azure-stack-manage-storage-accounts.md#reclaim)的 "回收容量" 一节。

### <a name="migrate-a-container-between-volumes"></a>在卷之间迁移容器
*此选项仅适用于 Azure Stack 集线器集成系统。*

由于租户使用模式，某些租户共享使用的空间比其他租户要多。 这可能会导致某些共享的空间在相对未使用的其他共享之前运行不足。

可以通过将某些 blob 容器手动迁移到其他共享来释放过度使用的共享上的空间。 可以将几个较小的容器迁移到单个共享上，使其拥有全部功能。 使用迁移来移动*可用*容器。 可用容器是不包含 VM 磁盘的容器。

迁移会在新的共享上合并容器的所有 blob。

- 如果容器已进入溢出模式并已将 blob 放置在其他卷上，则新共享必须具有足够的容量来容纳迁移的容器的所有 blob。 这包括位于其他共享上的 blob。

- PowerShell cmdlet `Get-AzsStorageContainer` 仅识别容器的初始卷上使用的空间。 Cmdlet 不会标识放在其他卷上的 blob 所使用的空间。 因此，容器的完整大小可能不明显。 新共享上的容器合并可能会将该新共享发送到溢出状态，在这种情况下，会将数据放置到其他共享上。 因此，你可能需要重新平衡共享。

- 如果你没有对某些资源组的权限，并且不能使用 PowerShell 来查询其他卷的溢出数据，请与这些资源组和容器的所有者合作，以了解在迁移之前要迁移的总数据量。  

> [!IMPORTANT]
> 容器的 blob 迁移是需要使用 PowerShell 的脱机操作。 在迁移完成之前，要迁移的容器的所有 blob 都保持脱机状态，并且不能使用。 还应避免升级 Azure Stack 中心，直到所有正在进行的迁移完成。

#### <a name="migrate-containers-by-using-powershell"></a>使用 PowerShell 迁移容器
1. 确认已[安装并配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关详细信息，请参阅[使用 Azure PowerShell 管理 Azure 资源](https://go.microsoft.com/fwlink/?LinkId=394767)。
2. 检查容器以了解计划迁移的共享上的数据。 若要确定卷中迁移的最佳候选容器，请使用 `Get-AzsStorageContainer` cmdlet：

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

5. 使用作业 ID 检查迁移作业的状态。 容器迁移完成后， **MigrationStatus**设置为*完成*。

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![示例：迁移状态](media/azure-stack-manage-storage-shares/migration-status1.png)

6. 你可以取消正在进行的迁移作业。 已取消异步处理已取消的迁移作业。 您可以使用 $jobid 跟踪取消：

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![示例：回滚状态](media/azure-stack-manage-storage-shares/rollback.png)

7. 你可以再次运行步骤6中的命令，直到迁移状态为 "已*取消*"：  

    ![示例：已取消状态](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>移动 VM 磁盘
*此选项仅适用于 Azure Stack 集线器集成系统。*

管理空间的最极端方法涉及移动 VM 磁盘。 由于移动附加容器（一个包含 VM 磁盘的容器）非常复杂，因此请联系 Microsoft 支持部门来完成此操作。

## <a name="next-steps"></a>后续步骤
若要详细了解如何向用户提供 Vm，请参阅[管理 Azure Stack 集线器的存储容量](azure-stack-tutorial-tenant-vm.md)。
