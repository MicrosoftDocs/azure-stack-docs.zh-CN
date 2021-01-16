---
title: 使用 SDN Express 部署 SDN 基础结构
description: 了解如何使用 SDN Express 部署 SDN 基础结构
author: v-dasis
ms.topic: how-to
ms.date: 12/16/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 968782c4fd5ac0f4e057815c526fbde8f90164af
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254834"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>使用 SDN Express 部署 SDN 基础结构

> 适用于 Azure Stack HCI 版本 20H2

本主题介绍如何使用 SDN Express PowerShell 脚本将端到端软件定义的网络 (SDN) 基础结构部署。 该基础结构包括高度可用的 (HA) 网络控制器、高度可用的软件负载均衡器 (SLB) 和高度可用的网关。  

脚本支持分阶段部署，在此部署中，你只需部署网络控制器即可实现核心功能集，同时提供最低程度的网络要求。 还可以使用 Windows Admin Center 中的“创建群集”向导部署网络控制器。 若要部署其他 SDN 组件（例如 SLB 和网关），必须使用 SDN Express 脚本。

你还可以使用 System Center Virtual Machine Manager (VMM) 部署 SDN 基础结构。 有关详细信息，请参阅 [在 VMM 构造中管理 SDN 资源](/system-center/vmm/network-sdn)。

## <a name="before-you-begin"></a>准备阶段

在开始 SDN 部署之前，请规划并配置物理和主机网络基础结构。 参考以下文章：

- [物理网络要求](../concepts/physical-network-requirements.md)
- [主机网络要求](../concepts/host-network-requirements.md)
- [使用 Windows 管理中心创建群集](create-cluster.md)
- [使用 Windows PowerShell 创建群集](create-cluster-powershell.md)
- [规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)

不必部署所有 SDN 组件。 请参阅[规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)中的[分阶段部署](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment)部分来确定所需的基础结构组件，并相应地运行脚本。

请确保所有主机服务器都安装了 Azure Stack HCI 操作系统。 有关如何执行此操作，请参阅 [部署 AZURE STACK HCI 操作系统](operating-system.md) 。

## <a name="run-the-sdn-express-scripts"></a>运行 SDN Express 脚本

1. 请参阅 [SDN Express](https://github.com/microsoft/SDN) GitHub 存储库。

1. 将文件从存储库下载到指定的部署计算机。 选择 " **克隆** " 或 " **下载 ZIP**"。

    > [!NOTE]
    > 指定的部署计算机必须运行 Windows Server 2016 或更高版本。

1. 提取 ZIP 文件，并将 `SDNExpress` 文件夹复制到部署计算机的 `C:\` 文件夹。

1. 导航到 `C:\SDNExpress\scripts` 文件夹。

1. 运行 `SDNExpress.ps1` 脚本文件。 此脚本使用 PowerShell 部署 (PSD) 文件作为不同配置设置的输入。 `README.md`有关如何运行脚本的说明，请参阅该文件。  

1. 使用包含 Azure Stack HCI OS 的 VHDX 作为创建 SDN Vm 的源。 如果已从 ISO 下载并安装了 Azure Stack HCI OS，则可以使用该实用程序创建此 VHDX， `convert-windowsimage` 如文档中所述。

1. `SDNExpress\scripts\MultiNodeSampleConfig.psd1`通过更改特定值来自定义文件，以适合您的基础结构，包括主机名、域名、用户名和密码以及 "[规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)" 主题中所述的网络的网络信息。 此文件包含有关根据是只部署网络控制器组件还是同时部署软件负载平衡器和网关组件而需要填写的内容的具体信息。

1. 从 Hyper-v 主机上具有管理员凭据的用户帐户中运行以下脚本：

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. 创建网络控制器 Vm 后，请在 DNS 服务器上为网络控制器群集名称配置动态 DNS 更新。 有关详细信息，请参阅 [部署网络控制器的要求](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)的步骤3。

## <a name="next-steps"></a>后续步骤

管理 Vm。 有关详细信息，请参阅 [管理 vm](../manage/vm.md)