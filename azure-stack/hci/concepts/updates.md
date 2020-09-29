---
title: 更新和升级
description: 有关如何向 Azure Stack HCI 应用更新和升级的概述。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: f56853ce64575d32ae10ccf7ac2aa0a1a640ec5b
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867190"
---
# <a name="updates-and-upgrades"></a>更新和升级

> 适用于：Azure Stack HCI，版本 20H2

Azure Stack HCI 解决方案旨在实现可预测的更新和升级体验。

## <a name="support-period"></a>支持期

Microsoft 硬件解决方案合作伙伴预期提供至少五年的硬件服务、支持和安全更新。 Microsoft 为 Azure Stack HCI 操作系统提供两年的支持，并提供年度功能更新，客户最多有一年时间来部署这些更新。

:::image type="content" source="media/updates/lifecycle-versions.png" alt-text="Azure Stack HCI 年度发布路线图" border="false":::

## <a name="updating-azure-stack-hci"></a>更新 Azure Stack HCI

与 Windows 10 一样，Azure Stack HCI 将借助 Microsoft 提供的每月质量和安全更新，始终保持最新状态。 使用“群集感知更新”启动更新，与 Windows Server 上一样。

硬件合作伙伴和解决方案构建者可以插入 Windows Admin Center 并开发扩展，以使服务器的固件、驱动程序和 BIOS 保持最新，并在群集节点之间保持一致。 购买预装了 Azure Stack HCI 的集成系统的客户可以通过这些扩展来安装解决方案更新；仅购买经验证的硬件节点的客户可能需要根据硬件供应商的建议单独执行更新。

虚拟机也需要定期更新。 除了 Windows 更新和 Windows Server Update Services 以外，还可以使用 Azure 更新管理来更新 VM。

## <a name="upgrading-to-azure-stack-hci"></a>升级到 Azure Stack HCI

你可以从 Windows Server 2019 升级到 Azure Stack HCI，但不是无缝升级；目前没有就地升级。 升级需要每个服务器留出一个维护时段和进行清洁安装，并在滚动群集升级过程中分别重新生成每个节点。

## <a name="cluster-aware-updating"></a>Cluster-Aware Updating

更新群集需要协调业务流程，因为某些更新需要重新启动操作系统，并且必须一次重新启动一个服务器，以确保群集的可用性。 群集感知更新 (CAU) 是 Microsoft 的默认群集业务流程协调程序，可实现通过 Windows Admin Center 中的集成更新体验执行更新，或通过 PowerShell 命令手动执行更新。 群集感知更新功能可进行扩展，扩展后可支持检索固件和驱动程序更新的合作伙伴插件。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [更新 Azure Stack HCI 群集](../manage/update-cluster.md)
