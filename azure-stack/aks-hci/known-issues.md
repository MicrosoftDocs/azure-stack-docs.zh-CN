---
title: Azure Stack HCI 上的 Azure Kubernetes 服务的已知问题
description: Azure Stack HCI 上的 Azure Kubernetes 服务的已知问题
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 5865c7cfea0231c811199ff4abbc05011e4d0a66
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840042"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Azure Stack HCI 上的 Azure Kubernetes 服务公共预览版的已知问题
本文介绍 Azure Stack HCI 上的 Azure Kubernetes 服务公共预览版的已知问题。

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>从 Azure Stack HCI 上的 AKS 失败部署中恢复
如果遇到部署问题或者要重置部署，请确保先关闭连接到 Azure Stack HCI 上的 Azure Kubernetes 服务的所有 Windows Admin Center 实例，然后从 PowerShell 管理窗口运行 Uninstall-AksHci。

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>使用 kubectl 删除节点时，可能未删除关联 VM
如果执行以下步骤，则会遇到此问题：
* 创建 Kubernetes 群集
* 将群集扩展到两个以上的节点
* 使用“kubectl delete node <节点名称>”删除节点 
* 运行 kubectl get nodes。 输出中未列出已删除的节点
* 打开 PowerShell 管理窗口
* 运行 get-vm。 仍会列出已删除的节点

这会导致系统无法识别节点缺失，并且不会启动新节点。 将来的版本将会解决此问题

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>必须在所有物理群集节点间和 Hyper-V 中配置时间同步
若要确保 gMSA 和 AD 身份验证正常工作，请确保将 Azure Stack HCI 群集节点配置为将其时间与域控制器或其他时间源同步，并将 Hyper-V 配置为将时间同步到任何虚拟机。

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>已加入域的 Azure Stack HCI 节点需要特殊 Active Directory 权限 
部署和配置 Azure Stack HCI 上的 Azure Kubernetes 服务的用户需要具有“完全控制”权限，才能在创建服务器和服务对象的 Active Directory 容器中创建 AD 对象。 

## <a name="get-akshcilogs-command-may-fail"></a>Get-AksHciLogs 命令可能会失败
对于大型群集，Get-AksHciLogs 命令可能会引发异常、未能枚举节点或不会生成 c:\wssd\wssdlogs.zip 输出文件。
这是因为用于压缩文件 `Compress-Archive` 的 PowerShell 命令的输出文件大小限制为 2 GB。 将来的版本将会解决此问题。

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>Azure Kubernetes Service PowerShell 部署在创建新目标群集之前未检查可用内存
创建 Kubernetes 节点之前，Aks-Hci PowerShell 命令不会验证主机服务器上的可用内存。 这可能会导致内存耗尽，虚拟机无法启动。 当前未正常处理此失败，部署将停止响应，且不会显示清楚的错误消息。
如果部署已停止响应，请打开 `Eventviewer`，并检查是否有 Hyper-V 相关错误消息指出没有足够的内存来启动 VM。
将来的版本将会解决此问题

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>在配置了静态 IP、VLAN、SDN 或代理的 Azure Stack HCI 上，Azure Kubernetes 服务部署失败。
在部署具有静态 IP、VLAN、SDN 或代理的 Azure Stack HCI 上的 Azure Kubernetes 服务群集时，部署会在群集创建时失败。 将来的版本将会解决此问题。

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>必须在宿主环境中禁用 IPv6
如果 IPv4 和 IPv6 地址都绑定到物理 NIC，则群集的 `cloudagent` 服务会使用 IPv6 地址进行通信。 部署框架中的其他组件仅使用 IPv4。 这会导致 Windows Admin Center 无法连接到群集，会在尝试连接到计算机时报告远程处理失败。
解决方法：在物理网络适配器上禁用 IPv6。
将来的版本将会解决此问题

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>在 Azure Stack HCI 群集节点之间移动虚拟机会迅速导致 VM 启动失败
在 Azure Stack HCI 群集中使用群集管理工具将 VM 从一个节点（节点 A）移动到另一个节点（节点 B）时，VM 可能无法在新节点上启动。 将 VM 移回原始节点之后，它也无法在其中启动。
发生此问题的原因是，用于清理首次迁移的逻辑是以异步方式运行。 因此，Azure Kubernetes 服务的“更新 VM 位置”逻辑会在节点 A 上的原始 Hyper-V 中找到 VM，并将它删除，而不是取消注册。
解决方法：确保 VM 在新节点上已成功启动，然后再将它移回原始节点。
将来的版本将会解决此问题

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>Azure Kubernetes 服务中的负载均衡器需要 DHCP 保留
Azure Stack HCI 上的 Azure Kubernetes 服务中的负载均衡解决方案使用 DHCP 将 IP 地址分配给服务终结点。 如果由于服务重启而导致服务终结点的 IP 地址发生更改，则 DHCP 租约会因为过期时间较短而过期。 因此，服务将无法访问，因为 Kubernetes 配置中的 IP 地址与终结点上的 IP 地址不同。 这可能会导致 Kubernetes 群集成为不可用状态。
若要解决此问题，请将 MAC 地址池用于负载均衡服务终结点，并为池中的每个 MAC 地址保留特定 IP 地址。
将来的版本将会解决此问题。

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>无法将 Azure Kubernetes 服务部署到具有单独存储和计算群集的环境
Windows Admin Center 不会将 Azure Kubernetes 服务部署到具有单独存储和计算群集的环境，因为它期望计算和存储资源由同一群集提供。 在大多数情况下，它不会查找计算群集公开的 CSV，会拒绝继续部署。
将来的版本将会解决此问题。

## <a name="windows-admin-center-only-supports-azure-kubernetes-service-for-azure-stack-hci-in-desktop-mode"></a>Windows Admin Center 仅支持桌面模式下的 Azure Stack HCI 的 Azure Kubernetes 服务
在预览版中，所有 Azure Stack HCI 的 Azure Kubernetes 服务功能仅在 Windows Admin Center 桌面模式下受支持。 Windows 10 电脑必须安装 Windows Admin Center 网关。 有关 Windows Admin Center 安装选项的详细信息，请访问 [Windows Admin Center 文档](/windows-server/manage/windows-admin-center/plan/installation-options)。 未来的版本将支持其他场景。

## <a name="azure-kubernetes-service-host-setup-fails-in-windows-admin-center-if-reboots-are-required"></a>如果需要重新启动，则 Azure Kubernetes 服务主机安装程序将在 Windows Admin Center 中失败
如果需要重新启动正在使用的一个或多个服务器以安装 PowerShell 或 Hyper-V 等角色，则 Azure Kubernetes 服务主机安装程序向导将失败。 当前的解决方法是退出向导，并在服务器重新联机后在同一系统上重试。 将来的版本将会解决此问题。

## <a name="azure-registration-step-in-azure-kubernetes-service-host-setup-asks-to-try-again"></a>Azure Kubernetes 服务主机安装程序中的 Azure 注册步骤要求重试
使用 Windows Admin Center 设置 Azure Kubernetes 服务主机时，在 Azure 注册页上输入所需信息后，可能会要求你再试一次。 你可能需要在 Windows Admin Center 网关上再次登录 Azure，以继续执行此步骤。 将来的版本将会解决此问题。

## <a name="windows-admin-center-doesnt-have-an-arc-offboarding-experience"></a>Windows 管理中心没有 Arc 脱离体验
Windows 管理中心当前没有从 Azure Arc 下架群集的过程。若要删除已销毁的群集上的 Arc aganets，请导航到 Azure 门户中群集的资源组，并手动删除弧线内容。 若要删除仍在运行的群集上的 Arc 代理，用户应运行以下命令：
```PowerShell
az connectedk8s delete
```

## <a name="when-setting-up-an-azure-kubernetes-service-host-using-windows-admin-center-setup-may-fail-if-file-explorer-is-open"></a>使用 Windows 管理中心设置 Azure Kubernetes 服务主机时，如果文件资源管理器处于打开状态，安装程序可能会失败
如果文件资源管理器是打开的，并且在到达 "检查和创建" 步骤时在 **C:\Program Files\AksHci** 目录中，则您的创建可能会失败，并出现错误 "进程无法访问文件 ' C:\Program Files\AksHci\wssdcloudagent.exe"。 这是因为它正被另一个进程使用。 若要避免此错误，请关闭文件资源管理器或导航到不同的目录，然后再执行此步骤。 

## <a name="cannot-connect-windows-admin-center-to-azure-as-create-new-azure-app-id-fails"></a>无法将 Windows 管理中心连接到 Azure，因为新建 Azure 应用 ID 失败
如果无法将 Windows 管理中心连接到 Azure，因为无法在网关上自动创建和使用 Azure 应用 ID，请创建 Azure 应用 ID，并为其分配对门户的正确权限。 然后，选择 **"在网关上使用现有的"**。 有关详细信息，请访问[将你的网关连接到 Azure。](/windows-server/manage/windows-admin-center/azure/azure-integration)
