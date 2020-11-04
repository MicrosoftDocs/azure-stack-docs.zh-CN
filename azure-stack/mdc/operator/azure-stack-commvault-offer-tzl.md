---
title: 在 Azure Stack 中提供 Commvault marketplace 项 |Microsoft Docs
description: 从 Azure Stack Marketplace 部署 Commvault
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 6ece6792602d90fd43bc682d7547061180ea9289
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328989"
---
# <a name="offer-commvault-marketplace-item-in-azure-stack"></a>在 Azure Stack 中提供 Commvault marketplace 项

*适用于：模块化数据中心、Azure Stack 中心耐用*

Commvault 支持 Azure Stack 集线器上的以下资源类型的备份和还原：

- VM 级别备份
  - IaaS VM
  - 非托管磁盘
  - 托管磁盘
  - 有关详细信息，请参阅 [备份虚拟机](https://documentation.commvault.com/commvault/v11/article?p=86503.htm)。

- 存储帐户备份
  - Blob
  - 有关详细信息，请参阅 [Azure Blob 存储概述](https://documentation.commvault.com/commvault/v11/article?p=30063.htm)。

- 基于代理的备份
  - 来宾操作系统-Windows 和 Linux
  - 应用程序--SQL、MySQL
  - 有关详细信息，请参阅 [备份代理](https://documentation.commvault.com/commvault/v11/article?p=14333.htm)。

可以在外部计算机上部署 Commvault，并远程保护 Azure Stack 集线器上的资源。 此外，还可以将 Commvault 部署为 Azure Stack 集线器上的虚拟设备。 有关 Commvault 的完整指南可在其文档站点上找到，其中包含 [Azure Stack 中心](https://documentation.commvault.com/commvault/v11/article?p=86486.htm)。 作为参考，Commvault 还发布了 [Microsoft Azure 功能的完整列表](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)。

## <a name="deploy-from-azure-stack-hub-marketplace"></a>从 Azure Stack 中心市场部署

Commvault 在 Azure Marketplace 中发布 BYOL 映像，并为联合 Azure Stack 集线器启用映像。 Azure Stack 上备份 Vm 所需的最低版本为 SP16。 如果你计划使用虚拟设备，请确保在 (SP16 中更新到最新的 [长期支持的版本](https://documentation.commvault.com/commvault/v11/article?p=2617.htm)) 或 SP17 (最新的主流版本) 。

| 云        | 版本 | 可用于联合 | 下一次更新 |
|--------------|---------|---------------------------|-------------|
| Azure 公共 | SP13    | 是                       | TBD         |
| Azure Gov    | SP13    | TBD                       | TBD         |

## <a name="download-from-azure-marketplace"></a>从 Azure Marketplace 下载

Azure Stack 中心操作员可将项目下载到本地 Azure Stack Marketplace，用于连接和断开连接的环境。 在连接的环境中，操作员可以浏览可用项列表，以便从 Azure 添加：

![从 Azure 添加](media/azure-stack-commvault-offer-tzl/add-from-azure.png)

## <a name="upload-and-publish-manually"></a>手动上传和发布

在断开连接的环境中，必须从 Azure 下载项目，然后手动将其上传到 Azure Stack 集线器。 有关详细信息，请参阅 [连接和断开连接环境的完整说明](../../operator/azure-stack-download-azure-marketplace-item.md)。

## <a name="deployment-considerations"></a>部署注意事项

- 将外部部署到 Azure Stack 中心
- 在 Azure Stack 集线器上部署为虚拟设备
- 磁盘库与云库
- 网络视线注意事项
- 订阅级别隔离

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何保护 IaaS Vm，请参阅保护 Azure Stack 集线器上的 Vm。
