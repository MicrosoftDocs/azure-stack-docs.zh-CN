---
title: 在 Azure Stack Hub 中添加缩放单元节点
description: 了解如何将缩放单元节点添加到 Azure Stack Hub 中的缩放单元。
author: mattbriggs
ms.topic: article
ms.date: 11/05/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/05/2020
ms.openlocfilehash: fac60db9ad1f3ae8be248b4f61a3c16179763a7e
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364194"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>在 Azure Stack Hub 中添加更多的缩放单元节点

可以通过添加额外的物理计算机来增加现有缩放单位的总容量。 物理计算机也称为缩放单元节点。 添加的每个新缩放单元节点在 CPU 类型、内存以及磁盘数目和大小方面必须与缩放单元中现有的节点相同。 由于体系结构限制，Azure Stack 集线器不支持删除缩放单元节点以进行缩减。 仅可通过添加节点来扩展容量。

若要添加缩放单位节点，请在 Azure Stack 集线器中登录，并 (OEM) 运行来自硬件设备制造商的工具。 OEM 工具在硬件生命周期主机 (HLH) 上运行，目的是确保新的物理计算机与现有节点的固件级别匹配。

以下流程图显示添加缩放单元节点的一般过程：

![添加缩放单元流](media/azure-stack-add-scale-node/add-node-flow.svg)
<br> *OEM 硬件供应商是否制定物理服务器机架放置规则和更新固件因支持合同而异。*

添加新节点的操作可能需要数小时或数天才能完成。 添加其他缩放单元节点时，对系统上运行的工作负载没有影响。

> [!NOTE]  
> 在添加缩放单元节点的操作已在进行时，请勿尝试任何下述操作：
>
>  - 更新 Azure Stack Hub
>  - 轮换证书
>  - 停止 Azure Stack Hub
>  - 修复缩放单元节点
>  - 添加另一个节点（前面的“添加节点”操作失败也被视为“正在进行中”）

## <a name="add-scale-unit-nodes"></a>添加缩放单元节点

以下步骤大致概述了如何添加节点。 请勿在未先参考 OEM 提供的容量扩展文档的情况下按照这些步骤操作。

1. 将新的物理服务器置于机架中并进行正确的布线。 
2. 启用物理交换机端口，并根据情况调整访问控制列表 (ACL)。
3. 根据 OEM 提供的文档，在基础板管理控制器 (BMC) 中配置正确的 IP 地址，并应用所有 BIOS 设置。
4. 使用硬件制造商提供的在 HLH 上运行的工具，将当前的固件基线应用于所有组件。
5. 在 Azure Stack Hub 管理员门户中运行“添加节点”操作。
6. 验证“添加节点”操作是否成功。 为此，请查看[缩放单元的“状态”  ](#monitor-add-node-operations)。 

## <a name="add-the-node"></a>添加节点

可以使用管理员门户或 PowerShell 来添加新节点。 “添加节点”操作首先将新的缩放单元节点添加为可用计算容量，然后自动扩展存储容量。 容量之所以可以自动扩展，是因为 Azure Stack Hub 是一个超聚合的系统，其中的计算和存储是一起缩放的。  

### <a name="administrator-portal"></a>[管理员门户](#tab/portal)

1. 以 Azure Stack Hub 操作员身份登录到 Azure Stack Hub 管理员门户。
2. 导航到“+ 创建资源”   > “容量”   > “缩放单元节点”  。
   ![缩放单元节点](media/azure-stack-add-scale-node/select-node1.png)
3. 在“添加节点”窗格中选择“区域”，然后选择要向其添加节点的“缩放单元”。    另请指定要添加的缩放单元节点的“BMC IP 地址”。  一次只能添加一个节点。
   ![添加节点详细信息](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="powershell-az"></a>[PowerShell Az](#tab/Az)

使用 **Add-AzsScaleUnitNode** cmdlet 添加一个节点。  

在使用下述某个示例 PowerShell 脚本之前，请将 name_of_new_node、name_of_scale_unit_cluster、BMCIP_address_of_new_node 值替换为你的 Azure Stack Hub 环境的值。

  > [!Note]  
  > 为节点命名时，必须确保名称的长度不到 15 个字符。 另外，不能使用包含空格或下述任何字符的名称：`\`、`/`、`:`、`*`、`?`、`"`、`<`、`>`、`|`、`\`、`~`、`!`、`@`、`#`、`$`、`%`、`^`、`&`、`(`、`)`、`{`、`}`、`_`。

**添加节点：**
  ```powershell
  ## Add a single Node 
    Add-AzsScaleUnitNode -BMCIPv4Address "<BMCIP_address_of_new_node>" -computername "<name_of_new_node>" -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

### <a name="powershell-azurerm"></a>[PowerShell AzureRM](#tab/AzureRM)

使用 **New-AzsScaleUnitNodeObject** cmdlet 添加一个节点。  

在使用下述某个示例 PowerShell 脚本之前，请将 *node names* 和 *IP addresses* 的值替换为你的 Azure Stack Hub 环境的值。

  > [!Note]  
  > 为节点命名时，必须确保名称的长度不到 15 个字符。 另外，不能使用包含空格或下述任何字符的名称：`\`、`/`、`:`、`*`、`?`、`"`、`<`、`>`、`|`、`\`、`~`、`!`、`@`、`#`、`$`、`%`、`^`、`&`、`(`、`)`、`{`、`}`、`_`。

**添加节点：**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

---

## <a name="monitor-add-node-operations"></a>监视“添加节点”操作 
使用管理员门户或 PowerShell 来获取“添加节点”操作的状态。 “添加节点”操作可能需要数小时或数天来完成。

### <a name="use-the-administrator-portal"></a>使用管理员门户 
若要监视添加新节点的操作，请在管理员门户中查看缩放单元或缩放单元节点对象。 为此，请转到“区域管理” > “缩放单元”。 接下来，选择要查看的缩放单元或缩放单元节点。 

### <a name="use-powershell"></a>使用 PowerShell
缩放单元和缩放单元节点的状态可以使用 PowerShell 来检索，如下所示：
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>“添加节点”操作的状态 
**对于缩放单元：**

|状态               |说明  |
|---------------------|---------|
|正在运行              |所有节点都积极参与缩放单元。|
|已停止              |缩放单元节点已停机或不可访问。|
|正在扩展            |当前正在将一个或多个缩放单元节点添加为计算容量。|
|配置存储  |计算容量已扩展，存储配置正在运行。|
|需要修正 |检测到错误，需要修复一个或多个缩放单元节点。|


**对于缩放单元节点：**

|状态                |说明  |
|----------------------|---------|
|正在运行               |节点都积极参与缩放单元。|
|已停止               |节点不可用。|
|正在添加                |正在主动将节点添加到缩放单元。|
|正在修复             |正在主动修复节点。|
|维护           |节点已暂停，没有处于运行状态的活动用户工作负荷。 |
|需要修正  |检测到错误，需要修复节点。|


## <a name="troubleshooting"></a>故障排除
下面是添加节点时的常见问题。 

**场景 1：**“添加缩放单元节点”操作失败，但一个或多个节点在列出时，其状态为“已停止”。  
- 修正：使用修复操作来修复一个或多个节点。 一次只能运行一个修复操作。

**场景 2：** 添加了一个或多个缩放单元节点，但存储扩展失败。 在这种情况下，缩放单元节点对象报告的状态为“正在运行”，但“配置存储”任务未启动。  
- 修正：通过运行以下 PowerShell cmdlet，使用特权终结点来查看存储运行状况：
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**场景 3：** 收到一个警报，指出存储横向扩展作业失败。  
- 修正：在这种情况下，存储配置任务已失败。 此问题需要联系支持部门。


## <a name="next-steps"></a>后续步骤 
[添加公共 IP 地址](azure-stack-add-ips.md) 
