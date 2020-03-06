---
title: ASDK 体系结构
description: 了解 Azure Stack 开发工具包（ASDK）体系结构。
author: justinha
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 207b99b9a3cbb6c030a6e79137d036820b3b3f60
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364073"
---
# <a name="asdk-architecture"></a>ASDK 体系结构
Azure Stack 开发工具包（ASDK）是在单个主机计算机上运行 Azure Stack 的单节点部署。 边缘路由组件安装在主计算机上，以提供 Azure Stack 的 NAT 和 VPN 功能。 Azure Stack 基础结构角色在物理主机计算机的 Hyper-v 层中运行。


## <a name="virtual-machine-roles"></a>虚拟机角色
ASDK 提供的服务使用开发工具包主机上托管的以下 Vm：

| 名称 | 说明 |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack 存储服务。|
| **AzS-ADFS01** | Active Directory 联合身份验证服务 (ADFS)。  |
| **AzS-CA01** | Azure Stack 角色服务的证书颁发机构服务。|
| **AzS-DC01** | Microsoft Azure Stack 的 Active Directory、DNS 和 DHCP 服务。|
| **AzS-ERCS01** | 紧急恢复控制台 VM。 |
| **AzS-GWY01** | 边缘网关服务，例如租户网络的 VPN 站点到站点连接。|
| **AzS-NC01** | 用于管理 Azure Stack 网络服务的网络控制器。  |
| **AzS-SLB01** | 对于租户和 Azure Stack 基础结构服务，在 Azure Stack 中负载平衡多路复用器服务。  |
| **AzS-SQL01-WIN12** | Azure Stack 基础结构角色的内部数据存储。  |
| **AzS-WAS01** | Azure Stack 管理员门户和 Azure 资源管理器服务。|
| **AzS-AZS-WASP01**| Azure Stack 用户（租户）门户和 Azure 资源管理器服务。|
| **AzS-AZS-XRP01** | Microsoft Azure Stack 的基础结构管理控制器，包括计算、网络和存储资源提供程序。|
| **AzS-SRNG01** | 支持承载 Azure Stack 的日志收集服务的环形 VM。 |

## <a name="next-steps"></a>后续步骤
[了解基本 ASDK 管理任务](asdk-admin-basics.md)
