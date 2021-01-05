---
title: 排查 Azure Stack Hub 上的 VM 已知问题
description: 了解如何排查 Azure Stack Hub 上的虚拟机已知问题
author: mattbriggs
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/09/2020
ms.openlocfilehash: d752b31ac5076255ae4368904145a50d5a3c09ff
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867156"
---
# <a name="known-issues-vms-on-azure-stack-hub"></a>已知问题：Azure Stack Hub 上的 VM

你可以在本文中找到在使用虚拟机 (VM) 和规模集时排查 Azure Stack Hub 计算资源提供程序期间出现的已知问题。

## <a name="portal-doesnt-show-correct-vm-name"></a>门户不显示正确的 VM 名称
- **适用于**  
    此问题适用于所有版本。  
- **原因**  
    在概述边栏选项卡中查看 VM 的详细信息时，计算机名称显示为“(不可用)”。 显示是针对从专用磁盘/磁盘快照创建的 VM 设计的。  
- **修正**  
    在门户中，选择“设置” > “属性” 。
- **出现次数**  
    通用  

## <a name="nvv4-vm-size-on-portal"></a>门户上的 NVv4 VM 大小
- **适用于**  
    此问题适用于 Azure Stack Hub 版本 2002 及更高版本。  
- **原因**  
    完成 VM 创建体验后，你将看到 VM 大小：NV4as_v4。 拥有基于 AMD MI25 的 Azure Stack Hub GPU 预览版所需的硬件的客户可以成功部署 VM。 所有其他客户将无法使用此 VM 大小部署 VM。  
- **修正**  
    无。  
- **出现次数**  
    通用  

## <a name="vm-boot-diagnostics"></a>VM 启动诊断
- **适用于**  
    此问题适用于所有支持的版本。  
- **原因**  
    创建新的虚拟机 (VM) 时，可能会显示以下错误：无法启动虚拟机 'vm-name'。 错误：无法更新 VM 'vm-name' 的串行输出设置。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。  
- **修正**  
    使用先前使用的相同名称重新创建存储帐户。
- **出现次数**  
    通用  

## <a name="vm-diagnostics-storage-account-not-found"></a>找不到 VM 诊断存储帐户
- **适用于**  
    此问题适用于所有支持的版本。  
- **原因**  
    尝试启动“已停止-解除分配”的虚拟机时，可能会显示以下错误：找不到 VM 诊断存储帐户 'diagnosticstorageaccount'。 请确保未删除存储帐户。 如果尝试在启用了启动诊断的情况下启动 VM，但引用的启动诊断存储帐户被删除，则会发生此错误。  
- **修正**  
    使用先前使用的相同名称重新创建存储帐户。  
- **出现次数** 常用  

## <a name="consumed-compute-quota"></a>已消耗的计算配额
- **适用于**  
    此问题适用于所有支持的版本。  
- **原因**   
    创建新虚拟机时，可能会收到一则错误消息，例如“此订阅在此位置的区域 vCPU 总数已达到上限。 此订阅使用了所有可用的 50 个区域 vCPU”。 这表示可用的核心配额总计已达到上限。  
- **修正**  
    请求操作员提供配额更高的附加计划。 更改当前计划的配额将不起作用，也不会反映提高的配额。
- **出现次数**  
    极少  

## <a name="virtual-machine-scale-set"></a>虚拟机规模集

-  **适用于**  
    此问题适用于所有支持的版本。  
- **原因**  
    在四节点的 Azure Stack Hub 环境中进行修补和更新时出现故障。 在包含三个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个四节点 Azure Stack Hub 环境中进行更新时出现 FabricVmPlacementErrorUnsupportedFaultDomainSize 错误。  
- **修正**  
    可以在包含两个容错域的可用性集中成功创建单一 VM。 但是，在四节点 Azure Stack Hub 部署中进行更新时，仍然不能创建规模集实例。  
- **出现次数**  
    极少  

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Stack Hub VM 功能](azure-stack-vm-considerations.md)的详细信息。
