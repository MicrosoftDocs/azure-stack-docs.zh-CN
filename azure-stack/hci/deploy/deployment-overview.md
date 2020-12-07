---
title: Azure Stack HCI 部署概述
description: Azure Stack HCI 部署过程的概述。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/7/2020
ms.openlocfilehash: cc8a994ac56dd773b34c1eb36295e252a9c9ad51
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761671"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>Azure Stack HCI 的部署过程是什么？

> 适用于：Azure Stack HCI，版本 20H2

本主题提供 Azure Stack HCI 部署过程的高级分步概述，并提供指向更详细信息的链接。

## <a name="plan"></a>计划

在部署之前，请仔细规划多站点群集（如果有）的存储、网络和相关要求。

### <a name="plan-storage"></a>规划存储

Azure Stack HCI 使用带有本地附加的驱动器的行业标准服务器来创建高度可用、高度可缩放的软件定义的存储。 为了满足性能和容量要求，请仔细[选择驱动器](../concepts/choose-drives.md)并[规划卷](../concepts/plan-volumes.md)。

### <a name="plan-networking"></a>规划网络

记录与部署相关的服务器名称、域名、RDMA 协议和版本以及 VLAN ID。

### <a name="plan-stretched-clusters"></a>规划延伸群集

如果 Azure Stack HCI 部署涉及多个站点，请确定每个站点需要多少服务器，以及群集配置是主动/被动还是主动/主动。 有关详细信息，请参阅[关于延伸群集](../concepts/stretched-clusters.md)。

## <a name="deploy"></a>部署

在部署 OS 之前，请确定硬件是否符合 Azure Stack HCI 的[系统要求](../concepts/system-requirements.md)。 然后，[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) 以管理 Azure Stack HCI 群集。

### <a name="1-deploy-azure-stack-hci"></a>1.部署 Azure Stack HCI

[下载 Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)，并在要加入群集的每个服务器上部署 Azure Stack HCI [操作系统](operating-system.md)。 如果已从 [Azure Stack HCI 目录](https://aka.ms/azurestackhcicatalog)通过你喜欢的 Microsoft 硬件合作伙伴购买 Azure Stack HCI 集成系统解决方案硬件，则 Azure Stack HCI 操作系统应已预安装。 在这种情况下，可以跳过此步骤，继续执行 #2。

### <a name="2-create-the-cluster"></a>2.创建群集

使用 [Windows Admin Center](create-cluster.md) 或 [PowerShell](create-cluster-powershell.md) 创建故障转移群集。 出于本机灾难恢复和业务连续性目的，可以部署涉及两个地理位置不同的站点的[延伸群集](../concepts/stretched-clusters.md)。

### <a name="3-set-up-a-cluster-witness"></a>3.设置群集见证

所有群集都必须[设置见证资源](witness.md)。 双节点群集需要见证，这样其中任一服务器脱机就不会导致另一个节点不可用。 三个及更多节点的群集需要见证，才能承受两台服务器故障或脱机。 

### <a name="4-register-with-azure"></a>4.注册到 Azure

Azure Stack HCI 需要连接到 Azure。 若要将群集连接到 Azure，请参阅[向 Azure 注册 Azure Stack HCI](register-with-azure.md)。 注册后，群集会在后台自动进行连接。

### <a name="5-validate-the-cluster"></a>5.验证群集

创建和注册群集后，在群集进入生产环境之前，[运行群集验证测试](validate.md)以找出硬件或配置问题。

### <a name="6-deploy-storage"></a>6.部署存储

在单站点群集上[创建卷](../manage/create-volumes.md)，或[在延伸群集上创建卷并设置复制](../manage/create-stretched-volumes.md)。

### <a name="7-deploy-workloads"></a>7.部署工作负载

现在你已了解如何[创建虚拟机](../manage/vm.md)和使用 Windows Admin Center 在 Azure Stack HCI 上部署工作负载。

## <a name="next-steps"></a>后续步骤

了解如何部署 Azure Stack HCI 操作系统。

> [!div class="nextstepaction"]
> [部署 Azure Stack HCI 操作系统](operating-system.md)
