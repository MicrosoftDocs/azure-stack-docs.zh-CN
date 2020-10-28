---
title: Azure Stack HCI 的已知问题
description: 本主题详细介绍 Azure Stack HCI 的已知问题。
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 05884ce83f6f35f8d75c632f67dabd38fa3c2814
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688319"
---
# <a name="known-issues-for-azure-stack-hci"></a>Azure Stack HCI 的已知问题

>适用于：Azure Stack HCI，版本 20H2

本主题详细介绍 Azure Stack HCI 的已知问题

- 2020年10月20日预览更新 (KB4580388) 可能导致群集感知更新 (CAU) 操作在 CAU 期间 (Vm) 的任何虚拟机在执行实时迁移时失败。 为避免出现这种情况，请暂时更改默认行为，以便 CAU 使用快速迁移而不是实时迁移。 有关详细信息，请参阅 [发行说明](release-notes.md#october-20-2020-preview-update-kb4580388) 。
- 本地群集与 Azure Stack HCI 云服务之间的连接尚不支持 [Azure Private Link](https://azure.microsoft.com/services/private-link)。
- Azure Stack HCI 云服务当前仅在选定区域可用。
- 以交互方式登录到操作系统时，"欢迎使用 Azure Stack HCI" 的 "欢迎使用" 屏幕尚未在捷克语、匈牙利语、Nederland、波兰语、瑞典语和土耳其语 (区域设置代码 cs-cz、hu-hu、nl-nl、) pl- 此外，在除英语之外的所有语言中，输入提示（例如 "[Y] es/[N] o"）仅接受值 "Y" 和 "N"，即使提示本身 misleadingly 显示为已本地化，如法语中的 [O] ui/[N] 或德语中的 [J] a/[N] ein。
- 如果使用嵌套虚拟化评估 Azure Stack HCI，你可能会遇到类似于 "由于虚拟化支持未启用而无法安装 Hyper-v" 的错误，因为 Azure Stack HCI 依赖于基于虚拟化的安全性。 有两种可能的解决方法： (1) 改为使用 Hyper-v 第1代 Vm;或 (2) 将 Hyper-v 功能插入到 VM 的 VHDX 脱机状态。 从主机处，在当 Azure Stack HCI 节点关闭时充当其节点的 VM 上运行以下 PowerShell 命令：Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell。
