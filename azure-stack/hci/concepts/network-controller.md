---
title: 规划部署网络控制器
description: 本主题介绍了如何计划通过 Windows 管理中心在一组虚拟机上部署网络控制器， (Vm) 运行 Azure Stack HCI 操作系统。
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 785665c9edc3af3230b4813e6da6bceddc43bd0a
ms.sourcegitcommit: b147d617c32cea138b5bd4bab568109282e44317
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90010826"
---
# <a name="plan-to-deploy-the-network-controller"></a>规划部署网络控制器

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019 

规划通过 Windows 管理中心部署网络控制器需要一组虚拟机 (Vm) 运行 Azure Stack HCI 操作系统。 网络控制器是高度可用且可伸缩的服务器角色，需要至少三个 Vm，才能在网络上提供高可用性。

   >[!NOTE]
   > 建议将网络控制器部署在其自己的专用 Vm 上。

## <a name="network-controller-requirements"></a>网络控制器要求
部署网络控制器需要以下各项：
- 用于创建网络控制器 Vm Azure Stack HCI 操作系统的 VHD。
- 用于将网络控制器 Vm 加入到域的域名和凭据。
- 与此部分中的两个拓扑选项之一匹配的物理网络配置。

    Windows 管理中心在 Hyper-v 主机中创建配置。 但是，根据以下两个选项之一，管理网络必须连接到主机物理适配器：

    **选项 1**：单个物理交换机将管理网络连接到主机上的物理管理适配器，以及虚拟交换机使用的物理适配器上的干线：

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="选项1：为网络控制器创建物理网络。" lightbox="./media/network-controller/topology-option-1.png":::

    **选项 2**：如果管理网络与工作负荷网络建立了物理隔离，则需要两个虚拟交换机：

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="选项2为网络控制器创建物理网络。" lightbox="./media/network-controller/topology-option-1.png":::

- 网络控制器用于与 Windows 管理中心和 Hyper-v 主机通信的管理网络信息。
- 网络控制器 Vm 的基于 DHCP 或静态网络寻址。
- 具象状态传输 (REST) 完全限定的域名 (FQDN) 用于管理客户端用于与网络控制器通信的网络控制器。

   >[!NOTE]
   > Windows 管理中心目前不支持网络控制器身份验证，可用于与 REST 客户端通信或网络控制器 Vm 之间的通信。 如果使用 PowerShell 进行部署和管理，则可以使用基于 Kerberos 的身份验证。

## <a name="configuration-requirements"></a>配置要求
可以在同一子网或不同子网中部署网络控制器群集节点。 如果计划在不同的子网中部署网络控制器群集节点，则必须在部署过程中提供网络控制器 REST DNS 名称。

若要了解详细信息，请参阅 [为网络控制器配置动态 DNS 注册](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)。


## <a name="next-steps"></a>后续步骤
现在，你已准备好在运行 Azure Stack HCI 操作系统的虚拟机上部署网络控制器。

若要了解更多信息，请参阅以下文章：
- [创建 Azure Stack HCI 群集](../deploy/create-cluster.md)

## <a name="see-also"></a>另请参阅
- [网络控制器](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [网络控制器高可用性](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)