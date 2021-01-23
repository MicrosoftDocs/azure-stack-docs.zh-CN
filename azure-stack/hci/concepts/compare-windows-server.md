---
title: 将 Azure Stack HCI 与 Windows Server 进行比较
description: 本主题将帮助你确定 Azure Stack HCI 或 Windows Server 是否适合你的组织。
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/21/2021
ms.openlocfilehash: c691424251d096794315880106c131ecaaea1f57
ms.sourcegitcommit: 925351b77490364b3d52746f788c4c1b93343631
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98705177"
---
# <a name="compare-azure-stack-hci-to-windows-server-2019"></a>将 Azure Stack HCI 与 Windows Server 2019 进行比较

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题介绍 Azure Stack HCI 和 Windows Server 2019 之间的主要区别，并提供有关何时使用每个的指导。 Microsoft 同时支持和维护两个产品。 许多组织可以选择同时部署这两个组织，因为它们适用于不同和互补的目的。

## <a name="when-to-use-azure-stack-hci"></a>何时使用 Azure Stack HCI

Azure Stack HCI 是 Microsoft 的顶级超聚合基础结构平台，用于在本地运行 Vm 或虚拟桌面，并连接到 Azure 混合服务。 这是一种使你的数据中心和分支机构实现现代化和保护的简单方法，还能实现低延迟和数据主权的业界最佳性能。


:::image type="content" source="media/compare-windows-server/hci-scenarios.png" alt-text="何时在 Windows Server 2019 上使用 Azure Stack HCI" border="false" lightbox="media/compare-windows-server/hci-scenarios.png":::


将 Azure Stack HCI 用于：

- 用于实现基础结构现代化的最佳虚拟化主机，适用于核心数据中心的现有工作负荷或针对分支机构和边缘位置的新兴要求
- 使用 Azure 订阅中的定期创新流和一组一致的工具和体验，轻松实现云的扩展性
- 超聚合基础结构的所有优点：更简单、更整合的数据中心体系结构，具有高速存储和网络

  >[!NOTE]
  >由于 Azure Stack HCI 旨在用作新式超聚合体系结构的 Hyper-v 虚拟化主机，因此不包括来宾权限。 因此，Azure Stack HCI 只获得直接运行少量服务器角色的许可，任何其他角色都必须在 Vm 内部运行。

## <a name="when-to-use-windows-server-2019"></a>何时使用 Windows Server 2019

Windows Server 2019 是一个高度多样的多用途操作系统，具有数十个角色和数百个功能，包括来宾权限。 Windows Server 计算机可以在云中或本地，包括虚拟化在 Azure Stack HCI 之上。


:::image type="content" source="media/compare-windows-server/windows-server-scenarios.png" alt-text="何时通过 Azure Stack HCI 使用 Windows Server 2019" border="false" lightbox="media/compare-windows-server/windows-server-scenarios.png":::


将 Windows Server 2019 用于：

- 虚拟机中的来宾操作系统 (Vm) 或容器
- 作为 Windows 应用程序的运行时
- 使用一个或多个内置服务器角色，如 Active Directory、文件服务、DNS、DHCP 或 Internet Information Services (IIS) 
- 作为传统服务器（例如裸机域控制器或 SQL Server 安装）
- 对于传统基础结构（例如连接到光纤通道 SAN 存储的 Vm）

## <a name="compare-product-positioning"></a>比较产品定位

下表显示 Azure Stack HCI 和 Windows Server 2019 的高级产品打包。

| **Attribute**    | **Azure Stack HCI** | **Windows Server 2019** |
| ---------------- | ------------------- | ----------------------- |
| 产品类型     | 云服务，其中包含一个或多个操作系统 | 操作系统 |
| Legal            | 涵盖 Microsoft 客户协议或联机订阅协议 | 具有其自己的最终用户许可协议 |
| 许可        | 向你的 Azure 订阅计费 | 有自己的付费许可证 |
| 支持          | 受 Azure 支持 | 可由不同的支持协议（包括 Microsoft 顶级支持）涵盖 |
| 从何处获取  | 从 [Azure.com/HCI](https://azure.com/hci)下载，或在集成系统上预安装 | Microsoft 批量许可服务中心或评估中心 |
| 在 Vm 中运行      | 仅适用于评估;用于主机操作系统 | 是，在云中或本地 |
| 硬件         | 在来自[AZURE STACK HCI 目录](https://hcicatalog.azurewebsites.net)的任何超过200的预验证解决方案上运行 | 在具有 "经 Windows Server 2019 认证" 徽标的任何硬件上运行 |
| 生命周期策略 | 始终保持最新功能 | 在 [Windows Server 服务通道](/windows-server/get-started-19/servicing-channels-19)之间进行选择： Long-Term 服务通道 (LTSC) 和 Semi-Annual 通道 (SAC)  |

## <a name="compare-technical-features"></a>比较技术功能

下表对 Azure Stack HCI 和 Windows Server 2019 的技术功能进行了比较。

| **Attribute** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| 核心 Hyper-v | 是 | 是 |
| 核心存储空间直通 | 是 | 是 |
| 核心 SDN | 是 | 是 |
| 用于灾难恢复的 Stretch 群集 | 是 | - |
| 存储空间修复速度提高4倍 | 是 | - |
| 集成驱动程序和固件更新 | 是 (集成系统仅)  | - |
| 引导式部署 | 是 | - |

## <a name="compare-management-options"></a>比较管理选项

下表比较了 Azure Stack HCI 和 Windows Server 2019 的管理选项。 这两种产品都设计用于远程管理，并且可以通过许多相同的工具进行管理。

| **Attribute** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| 桌面体验 | - | 是 |
| Windows Admin Center | 是 | 是 |
| Microsoft System Center | 是 (单独销售)  | 是 (单独销售)  |
| Azure 门户 | 是 (本机)  | 需要 Arc 代理 |
| 第三方工具 | 是 | 是 |

## <a name="compare-product-pricing"></a>比较产品定价

下表比较了 Azure Stack HCI 和 Windows Server 2019 的产品定价。 有关详细信息，请参阅 [AZURE STACK HCI 定价](https://azure.microsoft.com/pricing/details/azure-stack/hci/)。

| **Attribute** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| 价格类型 | 订阅服务 | 变化：最常使用一次性许可证 |
| 价格结构 | 每个内核，每月 | 变化：通常每个核心 |
| 价格 | 每月 $10 美元，每月 | 请参阅 [Windows Server 2019 的定价和许可](https://www.microsoft.com/windows-server/pricing) |
| 评估/试用期 | 注册30天免费试用版 | 180天评估版 |
| 信道 | 企业协议、云服务提供商或直接 | 企业协议/批量许可、OEM、服务提供商许可协议 (SPLA)  |

## <a name="next-steps"></a>后续步骤

- [将 Azure Stack HCI 与 Azure Stack 中心进行比较](compare-azure-stack-hub.md)
