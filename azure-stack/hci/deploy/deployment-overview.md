---
title: Azure Stack HCI 部署概述
description: Azure Stack HCI 部署过程的概述。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/09/2020
ms.openlocfilehash: 34a93a65d45861c7c7ff1727347cc95465968151
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572511"
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

### <a name="1-before-you-begin"></a>1.准备阶段

开始之前，对于 Azure Stack HCI 的部署，请[确定你的硬件是否满足基本要求并收集所需信息](before-you-start.md)。 然后，[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) 以管理 Azure Stack HCI 群集。

### <a name="2-deploy-azure-stack-hci"></a>2.部署 Azure Stack HCI

[下载 AZURE STACK hci](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) ，并在要进行群集的每个服务器上部署 Azure Stack hci [操作系统](operating-system.md) 。

### <a name="3-create-the-cluster"></a>3.创建群集

使用 [Windows Admin Center](create-cluster.md) 或 [PowerShell](create-cluster-powershell.md) 创建故障转移群集。 出于本机灾难恢复和业务连续性目的，可以部署涉及两个地理位置不同的站点的[延伸群集](../concepts/stretched-clusters.md)。

### <a name="4-set-up-a-cluster-witness"></a>4.设置群集见证

所有群集都必须[设置见证资源](witness.md)。 双节点群集需要见证，这样其中任一服务器脱机就不会导致另一个节点不可用。 三个及更多节点的群集需要见证，才能承受两台服务器故障或脱机。 

### <a name="5-register-with-azure"></a>5.注册到 Azure

Azure Stack HCI 需要连接到 Azure。 若要将群集连接到 Azure，请参阅[向 Azure 注册 Azure Stack HCI](register-with-azure.md)。 注册后，群集会在后台自动进行连接。

### <a name="6-validate-the-cluster"></a>6.验证群集

创建和注册群集后，在群集进入生产环境之前，[运行群集验证测试](validate.md)以找出硬件或配置问题。

### <a name="7-deploy-storage"></a>7.部署存储

在单站点群集上[创建卷](../manage/create-volumes.md)，或[在延伸群集上创建卷并设置复制](../manage/create-stretched-volumes.md)。

### <a name="8-deploy-workloads"></a>8.部署工作负载

现在你已了解如何[创建虚拟机](../manage/vm.md)和使用 Windows Admin Center 在 Azure Stack HCI 上部署工作负载。

## <a name="next-steps"></a>后续步骤

了解部署 Azure Stack HCI 之前需要执行的操作。

> [!div class="nextstepaction"]
> [开始之前](before-you-start.md)
