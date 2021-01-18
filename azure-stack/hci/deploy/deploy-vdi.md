---
title: Azure Stack HCI 上部署 (VDI) 的虚拟桌面基础结构
description: 本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署虚拟桌面基础结构 (VDI) 的指导。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: dfe22eb768b5bf661760c31c913d8c93caf590a4
ms.sourcegitcommit: 48a46142ea7bccd6c8a609e188dd7f3f6444f3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562892"
---
# <a name="deploy-virtual-desktop-infrastructure-vdi-on-azure-stack-hci"></a>Azure Stack HCI 上部署 (VDI) 的虚拟桌面基础结构

>适用于：Azure Stack HCI，版本 20H2

本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署虚拟桌面基础结构 (VDI) 的指导。 利用你的 Azure Stack HCI 投资为组织中的用户提供集中、高度可用、简化和安全的管理。 使用本指南来实现用户的自带设备 (BYOD) 等方案，同时为他们提供对业务关键应用程序的一致且可靠的体验，而不会牺牲安全性。

## <a name="overview"></a>概述
VDI 使用服务器硬件在虚拟机 (VM) 上运行桌面操作系统和软件程序。 通过这种方式，VDI 允许在集中式服务器上运行传统桌面工作负载。 业务设置中的 VDI 优势包括在安全的数据中心保留敏感的公司应用程序和数据，并满足 BYOD 策略，而无需担心如何将个人数据与公司资产混合使用。 VDI 也成为支持远程和分支机构工作人员的标准，并提供对承包商和合作伙伴的访问权限。

Azure Stack HCI 为 VDI 提供最佳平台。 经验证的 Azure Stack HCI 解决方案结合了 Microsoft [远程桌面服务 (RDS) ](/windows-server/remote/remote-desktop-services/welcome-to-rds) 使你可以实现高度可用且高度可缩放的体系结构。

此外，Azure Stack HCI VDI 提供以下基于云的独特功能来保护 VDI 工作负荷和客户端：
- 使用 Azure 更新管理的集中管理的更新
- 为 VDI 客户端提供统一的安全管理和高级威胁防护

## <a name="deploy-vdi"></a>部署 VDI
本部分详细介绍如何获取硬件以在 Azure Stack HCI 上部署 VDI 并使用 Windows 管理中心进行管理。 还介绍了如何部署 RDS 以支持 VDI。

### <a name="step-1-acquire-hardware-for-vdi-on-azure-stack-hci"></a>步骤1：在 Azure Stack HCI 上获取 VDI 的硬件
首先，需要购买硬件。 要执行此操作，最简单的方法是在 [AZURE STACK HCI 目录](https://hcicatalog.azurewebsites.net) 中查找首选的 Microsoft 硬件合作伙伴，并使用预装的 Azure Stack HCI 操作系统购买集成系统。 在目录中，可以进行筛选，以查看针对此类型的工作负荷进行了优化的供应商硬件。 请务必咨询硬件合作伙伴，以确保硬件可以支持要在群集上托管的虚拟机的数量。

否则，你将需要在你自己的硬件上部署 Azure Stack HCI 操作系统。 有关 Azure Stack HCI 部署选项和安装 Windows 管理中心的详细信息，请参阅 [部署 AZURE STACK HCI 操作系统](./operating-system.md)。

接下来，使用 Windows 管理中心 [创建 AZURE STACK HCI 群集](./create-cluster.md)。

### <a name="step-2-set-up-azure-update-management-in-windows-admin-center"></a>步骤2：在 Windows 管理中心中设置 Azure 更新管理
在 Windows 管理中心，设置 [Azure 更新管理](/windows-server/manage/windows-admin-center/azure/azure-update-management) 以快速评估可用更新的状态、计划所需的更新，并查看部署结果以验证应用的更新。

若要开始使用 Azure 更新管理，你需要订阅才能 Microsoft Azure。 如果没有订阅，可以注册 [免费试用版](https://azure.microsoft.com/free)。

你还可以使用 Windows 管理中心来设置其他 Azure 混合服务，例如备份、文件同步、Site Recovery、点到站点 VPN 和 Azure 安全中心。

### <a name="step-3-deploy-remote-desktop-services-rds-for-vdi-support"></a>步骤3：为 VDI 支持部署远程桌面服务 (RDS) 
完成 Azure Stack HCI 部署并向 Azure 注册以使用更新管理后，便可以使用本节中的指导来构建和部署 RDS 以支持 VDI。

若要生成和部署 RDS：
1. [部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)
1. 创建用于共享应用和资源[的 RDS 会话集合](/windows-server/remote/remote-desktop-services/rds-create-collection)
1. [为 RDS 部署授权](/windows-server/remote/remote-desktop-services/rds-client-access-license)
1. 指导用户安装 [远程桌面客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) 以访问应用和资源
1. 添加连接代理和会话主机以实现高可用性：
    - [通过 RD 会话主机场横向扩展现有 RDS 集合](/windows-server/remote/remote-desktop-services/rds-scale-rdsh-farm)
    - [为 RD 连接代理基础结构提供高可用性](/windows-server/remote/remote-desktop-services/rds-connection-broker-cluster)
    - [向 RD Web 和 RD 网关 Web 前端添加高可用性](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
    - [为 UPD 存储部署两个节点的存储空间直通文件系统](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)

## <a name="next-steps"></a>后续步骤
有关与 VDI 相关的详细信息，请参阅 [远程桌面服务支持的配置](/windows-server/remote/remote-desktop-services/rds-supported-config)
