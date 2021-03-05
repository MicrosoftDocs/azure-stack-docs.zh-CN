---
title: 在 Azure Stack HCI 上部署分支机构和边缘
description: 本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署分支机构和边缘方案的指南。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: ee9fef4a7da2312876eff168573b1a26bea0290b
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186834"
---
# <a name="deploy-branch-office-and-edge-on-azure-stack-hci"></a>在 Azure Stack HCI 上部署分支机构和边缘

>适用于：Azure Stack HCI，版本 20H2

本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署分支机构和边缘方案的指南。 本指南使你的组织能够在远程分支机构和边缘部署中的虚拟机 (Vm) 和容器中运行复杂、高度可用的工作负载。 边缘的计算将大多数数据处理从集中式系统转移到网络边缘，更接近需要快速使用数据的设备或系统。

使用 Azure Stack HCI 运行虚拟化应用程序，并在建议的硬件上运行高可用性的工作负荷。 硬件支持由两台服务器组成的群集，这些服务器使用存储的嵌套复原能力，一种简单的低成本 USB 拇指驱动器群集，并通过基于浏览器的 Windows 管理中心进行管理。 有关创建 USB 设备群集见证服务器的详细信息，请参阅 [部署文件共享见证](/windows-server/failover-clustering/file-share-witness)。

Azure IoT Edge 将云分析和自定义业务逻辑移动到设备上，以便你可以专注于业务见解而不是数据管理。 Azure IoT Edge 将 AI、云和边缘计算组合到容器化云工作负荷中，如 Azure 认知服务、机器学习、流分析和函数。 工作负荷可以在设备上运行，范围从 Raspberry Pi 到聚合边缘服务器。 使用 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub) 管理边缘应用程序和设备。

将 Azure IoT Edge 添加到 Azure Stack HCI 分支办公室和边缘部署港务局环境，以支持 [CI/CD 管道](/azure/iot-edge/how-to-continuous-integration-continuous-deployment) 应用程序部署框架。 你的组织中的 DevOps 人员可以部署和循环构建的容器化应用程序，该应用程序是通过传统的 VM 管理过程和工具构建和支持的。

Azure IoT Edge 的主要功能：
- Microsoft 开源软件
- 在 Windows 或 Linux 上运行
- 在 edge 上运行以提供近乎实时的响应
- 保护软件和硬件机制
- 适用于 [Azure IoT Edge 的 AI 工具包](https://github.com/Azure/ai-toolkit-iot-edge)
- Open 可编程性支持： Java、.NET Core 2.0、Node.js、C 和 Python
- 脱机和间歇性连接支持
- Azure IoT 中心的本机管理

若要了解详细信息，请参阅 [什么是 Azure IoT Edge](/azure/iot-edge/about-iot-edge)。

## <a name="deploy-branch-office-and-edge"></a>部署分支机构和边缘
本部分详细介绍了如何在 Azure Stack HCI 上为分支机构和边缘部署获取硬件，以及如何使用 Windows 管理中心进行管理。 还介绍了如何部署 Azure IoT Edge 来管理云中的容器。

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>步骤1：从 Azure Stack HCI 目录获取硬件
首先，需要购买硬件。 要执行此操作，最简单的方法是在 [AZURE STACK HCI 目录](https://hcicatalog.azurewebsites.net) 中查找首选的 Microsoft 硬件合作伙伴，并使用预装的 Azure Stack HCI 操作系统购买集成系统。 在目录中，可以进行筛选，以查看针对此类型的工作负荷进行了优化的供应商硬件。

否则，你将需要在你自己的硬件上部署 Azure Stack HCI 操作系统。 有关 Azure Stack HCI 部署选项和安装 Windows 管理中心的详细信息，请参阅 [部署 AZURE STACK HCI 操作系统](./operating-system.md)。

接下来，使用 Windows 管理中心 [创建 AZURE STACK HCI 群集](./create-cluster.md)。

### <a name="step-2-set-up-azure-monitor-in-windows-admin-center"></a>步骤2：在 Windows 管理中心中设置 Azure Monitor
在 Windows 管理中心，设置 Azure Monitor，以便深入了解 Azure Stack HCI 分支机构和边缘部署的应用程序、网络和服务器运行状况。

若要了解详细信息，请参阅 [通过 Azure Monitor 监视 AZURE STACK HCI](../manage/azure-monitor.md)。

你还可以使用 Windows 管理中心来设置其他 Azure 混合服务，例如备份、文件同步、Site Recovery、点到站点 VPN、更新管理和安全中心。

### <a name="step-3-use-container-based-apps-and-iot-data-processing"></a>步骤3：使用基于容器的应用和 IoT 数据处理
现在，你已准备好使用基于容器的新式应用程序开发和 IoT 数据处理。 使用 Windows 管理中心执行本部分中的步骤，部署运行 Azure IoT Edge 的 VM。

若要了解详细信息，请参阅 [什么是 Azure IoT Edge](/azure/iot-edge/about-iot-edge)。

在 Azure Stack HCI 上部署 Azure IoT Edge：
1. 使用 Windows 管理中心 [在 AZURE STACK HCI 中创建新 VM](/windows-server/manage/windows-admin-center/use/manage-virtual-machines#create-a-new-virtual-machine)。

    有关支持的操作系统版本、VM 类型、处理器体系结构和系统要求的信息，请参阅 [Azure IoT Edge 支持的系统](/azure/iot-edge/support)。

1. 如果还没有 Azure 帐户，请启动一个 [免费帐户](https://azure.microsoft.com/free)。
1. 在 Azure 门户中， [创建 Azure IoT 中心](/azure/iot-edge/quickstart#create-an-iot-hub)。
1. 在 Azure 门户中， [注册 IoT Edge 设备](/azure/iot-edge/quickstart#register-an-iot-edge-device)。

    >[!NOTE]
    > IoT Edge 设备位于 Azure Stack HCI 上运行 Windows 或 Linux 的 VM 上。

1. 在步骤1中创建的 VM 上， [安装并启动 IoT Edge 运行时](/azure/iot-edge/quickstart#install-and-start-the-iot-edge-runtime)。

   >[!IMPORTANT]
   > 需要在步骤4中创建的设备字符串将运行时连接到 Azure IoT 中心。

1. 将[模块部署](/azure/iot-edge/quickstart#deploy-a-module)到 Azure IoT Edge。

    你可以从 Azure Marketplace 的 " [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) " 部分中创建预生成的模块并进行部署。

## <a name="next-steps"></a>后续步骤
有关分支机构和边缘以及 Azure IoT Edge 的详细信息，请参阅：
- [快速入门：将第一个 IoT Edge 模块部署到虚拟 Linux 设备](/azure/iot-edge/quickstart-linux?preserve-view=true&view=iotedge-2018-06)
- [快速入门：将第一个 IoT Edge 模块部署到 Windows 设备](/azure/iot-edge/quickstart?preserve-view=true&view=iotedge-2018-06)