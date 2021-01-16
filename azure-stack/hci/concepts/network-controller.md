---
title: 规划部署网络控制器
description: 本主题介绍如何规划通过 Windows Admin Center 在运行 Azure Stack HCI 操作系统的一组虚拟机 (VM) 上部署网络控制器。
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 10/7/2020
ms.openlocfilehash: 3ac17ca9cd54c2ec387ceb39cbda76f7cb539305
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255072"
---
# <a name="plan-to-deploy-network-controller"></a>规划部署网络控制器

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

规划通过 Windows Admin Center 部署网络控制器需要运行 Azure Stack HCI 操作系统的一组虚拟机 (VM)。 网络控制器是高度可用且可缩放的服务器角色，需要至少三个 VM 才能在网络上提供高可用性。

   >[!NOTE]
   > 我们建议将网络控制器部署在其自己的专用 VM 上。

## <a name="network-controller-requirements"></a>网络控制器要求
部署网络控制器需要以下各项：
- 用于创建网络控制器 VM 的 Azure Stack HCI 操作系统的虚拟硬盘 (VHD)。
- 用于将网络控制器 VM 加入到域的域名和凭据。
- 至少一个使用 Windows Admin Center 中的群集创建向导配置的虚拟交换机。
- 与此部分中的拓扑选项之一匹配的物理网络配置。

    Windows Admin Center 会在 Hyper-V 主机中创建配置。 但是，管理网络必须根据以下三个选项之一连接到主机物理适配器：

    **选项 1**：管理网络在物理上与工作负载网络隔离。 此选项将一个虚拟交换机同时用于计算和存储：

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="为网络控制器创建物理网络的选项 1。" lightbox="./media/network-controller/topology-option-1.png":::

    **选项 2**：管理网络在物理上与工作负载网络隔离。 此选项将一个虚拟交换机仅用于计算：

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="为网络控制器创建物理网络的选项 2。" lightbox="./media/network-controller/topology-option-2.png":::

    **选项 3**：管理网络在物理上与工作负载网络隔离。 此选项使用两个虚拟交换机，一个用于计算，另一个用于存储：

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="为网络控制器创建物理网络的选项 3。" lightbox="./media/network-controller/topology-option-3.png":::

- 你还可以将管理物理适配器组合到一起，以使用同一管理交换机。 在这种情况下，我们仍建议使用此部分中的选项之一。
- 管理网络信息，网络控制器用于与 Windows Admin Center 和 Hyper-V 主机通信。
- 用于网络控制器 VM 的基于 DHCP 或基于静态网络的寻址。
- 网络控制器的表述性状态转移 (REST) 完全限定的域名 (FQDN)，管理客户端用于与网络控制器通信。

   >[!NOTE]
   > Windows Admin Center 目前不支持将网络控制器身份验证用于与 REST 客户端的通信或是网络控制器 VM 之间的通信。 如果使用 PowerShell 进行部署和管理，则可以使用基于 Kerberos 的身份验证。

## <a name="configuration-requirements"></a>配置要求
你可以在同一子网或不同子网中部署网络控制器群集节点。 如果规划在不同子网中部署网络控制器群集节点，则必须在部署过程中提供网络控制器 REST DNS 名称。

若要了解详细信息，请参阅[为网络控制器配置动态 DNS 注册](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)。

## <a name="next-steps"></a>后续步骤
现在，你已准备好在运行操作系统的 Vm 上部署网络控制器。

若要了解更多信息，请参阅以下文章：
- [创建 Azure Stack HCI 群集](../deploy/create-cluster.md)
- [使用 Windows PowerShell 部署网络控制器](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)

## <a name="see-also"></a>另请参阅
- [网络控制器](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [网络控制器高可用性](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)