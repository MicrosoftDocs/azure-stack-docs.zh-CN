---
title: 规划部署网络控制器
description: 本主题介绍如何计划通过 Windows 管理中心在一组虚拟机上部署网络控制器，这些虚拟机 (Vm) 运行 Azure Stack HCI 操作系统。
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 10/7/2020
ms.openlocfilehash: ec9ddb62dc876fbd4b99ebc2c8e2a3af4a54e8a7
ms.sourcegitcommit: 9a91dbdaa556725f51bcf3d8e79a4ed2dd5a209f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91847651"
---
# <a name="plan-to-deploy-network-controller"></a>规划部署网络控制器

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

规划通过 Windows 管理中心部署网络控制器需要一组虚拟机 (Vm) 运行 Azure Stack HCI 操作系统。 网络控制器是高度可用且可伸缩的服务器角色，需要至少三个 Vm，才能在网络上提供高可用性。

   >[!NOTE]
   > 建议在其自己的专用 Vm 上部署网络控制器。

## <a name="network-controller-requirements"></a>网络控制器要求
部署网络控制器需要以下各项：
- 虚拟硬盘 (VHD) 用于 Azure Stack HCI 操作系统创建网络控制器 Vm。
- 用于将网络控制器 Vm 加入到域的域名和凭据。
- 至少一个使用 Windows 管理中心中的群集创建向导配置的虚拟交换机。
- 与此部分中的拓扑选项匹配的物理网络配置。

    Windows 管理中心在 Hyper-v 主机中创建配置。 但是，根据以下三个选项之一，管理网络必须连接到主机物理适配器：

    **选项 1**：管理网络与工作负荷网络是物理隔离的。 此选项将单个虚拟交换机用于计算和存储：

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="选项1：为网络控制器创建物理网络。" lightbox="./media/network-controller/topology-option-1.png":::

    **选项 2**：管理网络与工作负荷网络是物理隔离的。 此选项仅使用单个虚拟交换机进行计算：

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="选项1：为网络控制器创建物理网络。" lightbox="./media/network-controller/topology-option-2.png":::

    **选项 3**：管理网络与工作负荷网络是物理隔离的。 此选项使用两个虚拟交换机，一个用于计算，一个用于存储：

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="选项1：为网络控制器创建物理网络。" lightbox="./media/network-controller/topology-option-3.png":::

- 你还可以将管理物理适配器组合到一起使用同一管理交换机。 在这种情况下，我们仍建议使用此部分中的选项之一。
- 网络控制器用于与 Windows 管理中心和 Hyper-v 主机通信的管理网络信息。
- 网络控制器 Vm 的基于 DHCP 或静态网络寻址。
- 具象状态传输 (REST) 完全限定的域名 (FQDN) 用于网络控制器，管理客户端使用该域名与网络控制器通信。

   >[!NOTE]
   > Windows 管理中心目前不支持网络控制器身份验证，可用于与 REST 客户端通信或网络控制器 Vm 之间的通信。 如果使用 PowerShell 进行部署和管理，则可以使用基于 Kerberos 的身份验证。

## <a name="configuration-requirements"></a>配置要求
可以在同一子网或不同子网中部署网络控制器群集节点。 如果计划在不同的子网中部署网络控制器群集节点，则必须在部署过程中提供网络控制器 REST DNS 名称。

若要了解详细信息，请参阅 [为网络控制器配置动态 DNS 注册](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)。

## <a name="next-steps"></a>后续步骤
现在，你已准备好在运行操作系统的 Vm 上部署网络控制器。

若要了解更多信息，请参阅以下文章：
- [创建 Azure Stack HCI 群集](../deploy/create-cluster.md)
- [使用 Windows PowerShell 部署网络控制器](../deploy/network-controller-powershell.md)

## <a name="see-also"></a>请参阅
- [网络控制器](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [网络控制器高可用性](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)