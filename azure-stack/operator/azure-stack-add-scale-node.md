---
title: 在 Azure Stack 集线器中添加缩放单位节点
description: 了解如何将缩放单位节点添加到 Azure Stack 集线器中的缩放单位。
author: mattbriggs
ms.topic: article
ms.date: 1/16/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 8aa33cda9a657d3e0bcec9525f86bd792c855e65
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77688757"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>在 Azure Stack 集线器中添加其他缩放单位节点

Azure Stack 中心操作员可通过添加额外的物理计算机来增加现有缩放单位的总容量。 物理计算机也称为缩放单位节点。 你添加的每个新的缩放单位节点都必须在 CPU 类型、内存和磁盘数量以及大小与扩展单元中已存在的节点数相同。

若要添加缩放单位节点，你可以在 Azure Stack 集线器中操作，并从硬件设备制造商（OEM）运行工具。 OEM 工具在硬件生命周期主机（HLH）上运行，以确保新物理计算机的固件级别与现有节点相同。

以下流程图显示了添加缩放单位节点的一般过程：

![添加缩放单位流](media/azure-stack-add-scale-node/add-node-flow.png)
<br> *你的 OEM 硬件供应商是否制定物理服务器机架位置，并且更新固件会根据你的支持合同而有所不同。*

用于添加新节点的操作可能需要几个小时或几天才能完成。 添加其他缩放单位节点时，对系统上运行的工作负荷没有影响。

> [!Note]  
> 当 "添加缩放单位" 节点操作正在进行时，请勿尝试执行以下任一操作：
>
>  - 更新 Azure Stack 中心
>  - 轮换证书
>  - 停止 Azure Stack 集线器
>  - 修复缩放单位节点


## <a name="add-scale-unit-nodes"></a>添加缩放单元节点

以下步骤概括介绍了如何添加节点。 不要首先参考 OEM 提供的容量扩展文档，不要执行这些步骤。

1. 将新的物理服务器放在机架上，并适当地将其连接起来。 
2. 启用物理交换机端口并调整访问控制列表（Acl）（如果适用）。
3. 在基板管理控制器（BMC）中配置正确的 IP 地址，并根据 OEM 提供的文档应用所有 BIOS 设置。
4. 使用在 HLH 上运行的硬件制造商提供的工具，将当前固件基线应用于所有组件。
5. 在 Azure Stack 中心管理员门户中运行添加节点操作。
6. 验证 "添加节点" 操作是否成功。 为此，请检查[缩放单位的**状态**](#monitor-add-node-operations)。 

## <a name="add-the-node"></a>添加节点

可以使用管理员门户或 PowerShell 来添加新节点。 "添加节点" 操作首先将新的缩放单位节点添加为可用计算容量，然后自动扩展存储容量。 容量会自动扩展，因为 Azure Stack 集线器是超聚合系统，其中*计算*和*存储*在一起缩放。

### <a name="use-the-administrator-portal"></a>使用管理员门户

1. 以 Azure Stack 中心操作员身份登录到 Azure Stack 中心管理员门户。
2. 导航到 " **+ 创建资源**" > **容量** > **缩放单位节点**。
   ![缩放单位节点](media/azure-stack-add-scale-node/select-node1.png)
3. 在 "**添加节点**" 窗格上，选择 "*区域*"，然后选择要向其中添加节点的*缩放单位*。 同时为要添加的缩放单位节点指定*BMC IP 地址*。 一次只能添加一个节点。
   ![添加节点详细信息](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>使用 PowerShell

使用**AzsScaleUnitNodeObject** cmdlet 添加节点。  

使用以下任一示例 PowerShell 脚本之前，请将值*节点名称*和*IP 地址*替换为 Azure Stack 中心环境中的值。

  > [!Note]  
  > 命名节点时，必须使名称长度小于15个字符。 你还不能使用包含空格的名称，也不能包含以下任何字符： `\`、`/`、`:`、`*`、`?`、`"`、`<`、`>`、`|`、`\`、`~`、`!`、`@`、`#`、`$`、`%`、`^`、`&``(``)`。`{``}``_`

**添加节点：**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>监视添加节点操作 
使用管理员门户或 PowerShell 获取 "添加节点" 操作的状态。 添加节点操作可能需要几个小时才能完成。

### <a name="use-the-administrator-portal"></a>使用管理员门户 
若要监视新节点的添加，请在管理员门户中查看缩放单位或缩放单位节点对象。 为此，请参阅**区域管理** > **缩放单位**。 接下来，选择要查看的缩放单位或缩放单位节点。 

### <a name="use-powershell"></a>使用 PowerShell
可按如下所示使用 PowerShell 检索缩放单位和缩放单位节点的状态：
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>添加节点操作的状态 
**对于缩放单位：**

|状态               |说明  |
|---------------------|---------|
|正在运行              |所有节点正在积极地参与缩放单位。|
|已停止              |缩放单位节点已关闭或无法访问。|
|展开            |一个或多个缩放单位节点当前正作为计算容量添加。|
|配置存储  |计算容量已展开，存储配置正在运行。|
|需要更正 |检测到需要修复一个或多个缩放单位节点的错误。|


**对于缩放单位节点：**

|状态                |说明  |
|----------------------|---------|
|正在运行               |节点正在积极地参与缩放单位。|
|已停止               |节点不可用。|
|添加                |正在将节点主动添加到缩放单位。|
|修复             |当前正在修复节点。|
|维护           |节点已暂停，并且没有正在运行的活动用户工作负荷。 |
|需要更正  |检测到需要修复节点的错误。|


## <a name="troubleshooting"></a>故障排除
下面是添加节点时出现的常见问题。 

**方案1：** 添加缩放单位节点操作失败，但一个或多个节点的状态为 "已停止"。  
- 修正：使用修复操作修复一个或多个节点。 一次只能运行一个修复操作。

**方案2：** 已添加一个或多个缩放单位节点，但存储扩展失败。 在这种情况下，缩放单位节点对象会报告正在运行的状态，但不会启动配置存储任务。  
- 修正：通过运行以下 PowerShell cmdlet，使用特权终结点来查看存储运行状况：
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**方案3：** 您收到了一个警报，指出存储扩展作业失败。  
- 修正：在这种情况下，存储配置任务失败。 此问题要求你联系支持人员。


## <a name="next-steps"></a>后续步骤 
[添加公共 IP 地址](azure-stack-add-ips.md) 
