---
title: Azure Stack HCI 上的 Azure Kubernetes 服务的已知问题
description: Azure Stack HCI 上的 Azure Kubernetes 服务的已知问题
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 89324bbfe2c1f28e268a22a9b31c880daaddecd6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948791"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Azure Stack HCI 公共预览版中的 Azure Kubernetes 服务的已知问题
本文介绍 Azure Stack HCI 上的 Azure Kubernetes 服务的公共预览版本的已知问题。

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>从 Azure Stack HCI 部署上的失败的 AKS 恢复
如果遇到部署问题或者想要重置部署，请确保先关闭 Azure Stack HCI 上连接到 Azure Kubernetes 服务的所有 Windows 管理中心实例，然后再从 PowerShell 管理窗口运行 AksHci。

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>使用 kubectl 删除节点时，可能不会删除关联的 VM
如果执行以下步骤，将会解决此问题：
* 创建 Kubernetes 群集
* 将群集扩展到两个以上的节点
* 使用 kubectl delete 节点 <节点名> 删除节点 
* 运行 kubectl 获取节点。 输出中未列出已删除的节点
* 打开 PowerShell 管理窗口
* 运行获取 vm。 仍会列出已删除的节点

这会导致系统无法识别节点，并且不会启动新节点。 这将在未来版本中修复

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>必须在所有物理群集节点和 Hyper-v 中配置时间同步
若要确保 gMSA 和 AD 身份验证正常工作，请确保将 Azure Stack HCI 群集节点配置为将其时间与域控制器或其他时间源同步，并将 Hyper-v 配置为将时间同步到任何虚拟机。

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>Azure Stack HCI 节点的已加入域需要特殊 Active Directory 权限 
Azure Stack HCI 上部署和配置 Azure Kubernetes 服务的用户需要具有 "完全控制" 权限，才能在创建服务器和服务对象的 Active Directory 容器中创建 AD 对象。 

## <a name="get-akshcilogs-command-may-fail"></a>AksHciLogs 命令可能会失败
对于大型群集，AksHciLogs 命令可能会引发异常、无法枚举节点或不会生成 c:\wssd\wssdlogs.zip 输出文件。
这是因为压缩文件的 PowerShell 命令的 `Compress-Archive` 输出文件大小限制为 2 GB。 将来的版本将会解决此问题。

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>Azure Kubernetes Service PowerShell 部署在创建新的目标群集之前不会检查可用内存
在创建 Kubernetes 节点之前，Aks-Hci PowerShell 命令不会验证主机服务器上的可用内存。 这可能导致内存消耗和虚拟机无法启动。 当前未正常处理此失败，部署将挂起，但不会出现清楚的错误消息。
如果你的部署似乎已挂起，请打开 `Eventviewer` 并检查与 hyper-v 相关的错误消息，指出没有足够的内存来启动 VM。
此问题将在将来的版本中得到解决

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>在配置了静态 Ip、Vlan、SDN 或代理的 Azure Stack HCI 上，Azure Kubernetes 服务部署失败。
在具有静态 Ip、Vlan、SDN 或代理的 Azure Stack HCI 群集上部署 Azure Kubernetes 服务时，部署在群集创建时失败。 将来的版本将会解决此问题。

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>必须在宿主环境中禁用 IPv6
如果 IPv4 和 IPv6 地址都绑定到物理 NIC，则 `cloudagent` 群集服务将使用 IPv6 地址进行通信。 部署框架中的其他组件仅使用 IPv4。 这将导致 Windows 管理中心无法连接到群集，并将在尝试连接到计算机时报告远程处理失败。
解决方法：在物理网络适配器上禁用 IPv6。
此问题将在将来的版本中得到解决

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>在 Azure Stack HCI 群集节点之间移动虚拟机会迅速导致 VM 启动失败
当使用群集管理工具将 VM 从一个节点移 (节点 A) 到另一个节点 (节点 B) 在 Azure Stack HCI 群集中时，VM 可能无法在新节点上启动。 将 VM 移回原始节点后，它也将无法启动。
发生此问题的原因是，用于清理首次迁移的逻辑将以异步方式运行。 因此，Azure Kubernetes 服务的 "更新 VM 位置" 逻辑在节点 A 上的原始 Hyper-v 上查找 VM，并将其删除，而不是将其注销。
解决方法：确保 VM 在新节点上已成功启动，然后再将其移回原始节点。
此问题将在将来的版本中得到解决

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>Azure Kubernetes 服务中的负载均衡器需要 DHCP 保留
Azure Stack HCI 上的 Azure Kubernetes Service 中的负载均衡解决方案使用 DHCP 将 IP 地址分配到服务终结点。 如果由于服务重启导致服务终结点的 IP 地址发生更改，则 DHCP 租约会因为较短的过期时间而过期。 因此，该服务将无法访问，因为 Kubernetes 配置中的 IP 地址与终结点上的 IP 地址不同。 这可能会导致 Kubernetes 群集变得不可用。
若要解决此问题，请将 MAC 地址池用于负载平衡的服务终结点，并为池中的每个 MAC 地址保留特定的 IP 地址。
将来的版本将会解决此问题。

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>无法将 Azure Kubernetes 服务部署到具有单独的存储和计算群集的环境
Windows 管理中心不会将 Azure Kubernetes 服务部署到具有独立的存储和计算群集的环境中，因为它期望同一群集提供计算和存储资源。 在大多数情况下，它不会找到计算群集公开的 Csv，并将拒绝继续部署。
将来的版本将会解决此问题。
