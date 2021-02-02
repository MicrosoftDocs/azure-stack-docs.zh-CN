---
title: 使用 SDN Express 部署 SDN 基础结构
description: 了解如何使用 SDN Express 部署 SDN 基础结构
author: v-dasis
ms.topic: how-to
ms.date: 02/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b8431b7e2cf2cad3d238386619839a760b722042
ms.sourcegitcommit: d74f6d8630783de49667956f39cac67e1968a89d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99473185"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>使用 SDN Express 部署 SDN 基础结构

> 适用于 Azure Stack HCI 版本 20H2

本主题介绍如何使用 SDN Express PowerShell 脚本将端到端软件定义的网络 (SDN) 基础结构部署。 基础结构可能包含高可用性 (HA) 网络控制器 (NC) 、高可用软件负载平衡器 (SLB) 和高度可用的网关。  脚本支持分阶段部署，在此部署中，你只需部署网络控制器即可实现核心功能集，同时提供最低程度的网络要求。 

你还可以使用 System Center Virtual Machine Manager (VMM) 部署 SDN 基础结构。 有关详细信息，请参阅 [在 VMM 构造中管理 SDN 资源](/system-center/vmm/network-sdn)。

## <a name="before-you-begin"></a>在开始之前

在开始 SDN 部署之前，请规划并配置物理和主机网络基础结构。 参考以下文章：

- [物理网络要求](../concepts/physical-network-requirements.md)
- [主机网络要求](../concepts/host-network-requirements.md)
- [使用 Windows 管理中心创建群集](../deploy/create-cluster.md)
- [使用 Windows PowerShell 创建群集](../deploy/create-cluster-powershell.md)
- [规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)

不必部署所有 SDN 组件。 请参阅[规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)中的[分阶段部署](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment)部分来确定所需的基础结构组件，并相应地运行脚本。

请确保所有主机服务器都安装了 Azure Stack HCI 操作系统。 有关如何执行此操作，请参阅 [部署 AZURE STACK HCI 操作系统](../deploy/operating-system.md) 。

## <a name="requirements"></a>要求

成功的 SDN 部署必须满足以下要求：

- 所有主机服务器都必须启用 Hyper-v
- 所有主机服务器都必须加入到 Active Directory
- 必须创建虚拟交换机
- 对于配置文件中定义的子网和 Vlan，必须配置物理网络
- 在配置文件中指定的 VHDX 文件必须可从运行 SDN Express 脚本的部署计算机访问

## <a name="create-the-vdx-file"></a>创建 VDX 文件

SDN 使用包含 Azure Stack HCI OS 的 VHDX 文件作为 (Vm) 创建 SDN 虚拟机的源。 VHDX 中的操作系统版本必须与 Hyper-v 主机使用的版本相匹配。

如果已从 ISO 下载并安装了 Azure Stack HCI OS，则可以使用 " `Convert-WindowsImage` [脚本中心](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f)" 中所述的实用程序创建此 VHDX。

下面显示了一个使用的示例 `Convert-WindowsImage` ：

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>下载 GitHub 存储库

GitHub 中的 SDN Express 脚本文件。 第一步是在部署计算机上获取所需的文件和文件夹。

1. 请参阅 [SDN Express](https://github.com/microsoft/SDN) GitHub 存储库。

1. 将文件从存储库下载到指定的部署计算机。 选择 " **克隆** " 或 " **下载 ZIP**"。

    > [!NOTE]
    > 指定的部署计算机必须运行 Windows Server 2016 或更高版本。

1. 提取 ZIP 文件，并将 `SDNExpress` 文件夹复制到部署计算机的 `C:\` 文件夹。

## <a name="edit-the-configuration-file"></a>编辑配置文件

PowerShell `MultiNodeSampleConfig.psd1` 配置数据文件包含 SDN Express 脚本所需的所有参数和设置，作为各种参数和配置设置的输入。 此文件包含有关根据是只部署网络控制器组件还是同时部署软件负载平衡器和网关组件而需要填写的内容的具体信息。

有关详细信息，请参阅 [规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md) 主题。

让我们开始以下操作：

1. 导航到 `C:\SDNExpress\scripts` 文件夹。

1. 在偏好的文本编辑器中打开 `MultiNodeSampleConfig.psd1` 文件。

1. 更改特定参数值以适应您的基础结构。

## <a name="run-the-deployment-script"></a>运行部署脚本

SDN Express 脚本部署 SDN 基础结构。 脚本完成后，你的基础结构已准备就绪，可用于工作负荷部署。

1. `README.md`有关如何运行部署脚本的最新信息，请查看该文件。  

1. 从具有群集主机服务器的管理凭据的用户帐户运行以下命令：

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. 创建网络控制器 Vm 后，请在 DNS 服务器上为网络控制器群集名称配置动态 DNS 更新。 有关详细信息，请参阅 [部署网络控制器的要求](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)的步骤3。

## <a name="next-steps"></a>后续步骤

管理 Vm。 有关详细信息，请参阅 [管理 vm](../manage/vm.md)