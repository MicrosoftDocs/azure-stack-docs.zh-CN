---
title: 将 Azure 门户与 Azure Stack HCI 配合使用
description: 如何使用 Azure 门户查看和管理 Azure Stack HCI 群集。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/15/2020
ms.openlocfilehash: e261c54140ed62a7b2fd19588e2b3ffc3b3911b8
ms.sourcegitcommit: 0579942530284d63472fb346cf5ff55de328cea4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90560775"
---
# <a name="use-the-azure-portal-with-azure-stack-hci"></a>将 Azure 门户与 Azure Stack HCI 配合使用

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题说明如何连接到 Azure Stack HCI 群集全局视图的 Azure 门户的 Azure Stack HCI 部分。 即使物理基础结构在本地托管，也可以使用 Azure 门户来管理和监视群集。 基于云的监视无需维护本地监视系统和数据库，从而降低了基础结构的复杂性。 它还可通过将警报和其他信息直接上传到 Azure 来提高可伸缩性，这已每天管理数百万个对象。

## <a name="view-your-clusters-in-the-azure-portal"></a>查看 Azure 门户中的群集

注册 Azure Stack HCI 群集后，其 Azure 资源将显示在 Azure 门户中。 若要查看它，请先登录到 [Azure 门户](https://portal.azure.com)。 如果已将 [群集注册到 Azure](../deploy/register-with-azure.md)，应会看到一个新的资源组，其中包含群集的名称，并附加 "-rg"。 如果未显示 Azure Stack HCI 资源组，请从下拉菜单中搜索 "HCI" 并选择群集：

:::image type="content" source="media/azure-portal/azure-portal-search.png" alt-text="搜索 hci Azure 门户，查找 Azure Stack HCI 资源":::

Azure Stack HCI 服务的主页会列出所有群集及其资源组、位置和关联的订阅。

:::image type="content" source="media/azure-portal/azure-portal-home.png" alt-text="Azure 门户上的 Azure Stack HCI 服务的主页":::

单击 Azure Stack HCI 资源查看该资源的 "概述" 页，该页面显示群集和服务器节点的高级摘要。

:::image type="content" source="media/azure-portal/azure-portal-overview.png" alt-text="Azure 门户上的 Azure Stack HCI 资源的概述摘要页":::

## <a name="view-the-activity-log"></a>查看活动日志

活动日志提供群集上最近操作和事件的列表及其状态、时间、关联的订阅和启动用户。 可以按订阅、严重性、时间跨度、资源组和资源筛选事件。

:::image type="content" source="media/azure-portal/azure-portal-activity-log.png" alt-text="Azure 门户上的 Azure Stack HCI 资源的活动日志屏幕":::

## <a name="configure-access-control"></a>配置访问控制

使用访问控制来检查用户访问权限、管理角色以及添加和查看角色分配以及拒绝分配。

:::image type="content" source="media/azure-portal/azure-portal-iam.png" alt-text="Azure 门户上 Azure Stack HCI 资源的访问控制屏幕":::

## <a name="add-and-edit-tags"></a>添加和编辑标记

标记是名称/值对，可让你通过将相同的标记应用到多个资源和资源组，对资源进行分类并查看合并的账单。 标记名称不区分大小写，标记值区分大小写。 [了解有关标记的详细信息](/azure/azure-resource-manager/management/tag-resources)。

:::image type="content" source="media/azure-portal/azure-portal-tags.png" alt-text="添加或编辑 Azure 门户上 Azure Stack HCI 资源的标记":::

## <a name="compare-azure-portal-and-windows-admin-center"></a>比较 Azure 门户和 Windows 管理中心

与 Windows 管理中心不同，Azure Stack HCI 的 Azure 门户体验旨在实现全局缩放多群集监视。 使用下表来帮助你确定适合你需要的管理工具。 它们共同提供了一致的设计，在补充方案中非常有用。

| Windows Admin Center | Azure 门户 |
| --------------- | --------------- |
| 边缘-本地硬件和虚拟机 (VM) 管理，始终可用 | 大规模管理，其他功能 |
| 管理 Azure Stack HCI 基础结构 | 管理其他 Azure 服务 |
| 监视和更新各个群集 | 大规模监视和更新 |
| 手动 VM 预配和管理 | 来自 Azure Arc 的自助服务 Vm |

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)
- [使用 Azure Monitor 监视 Azure Stack HCI](azure-monitor.md)
