---
title: 更改 Azure Stack 集线器交换机配置的设置
description: 了解可在 Azure Stack 集线器交换机配置上更改的设置。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 9fcbd0203684afbbae9d11ed00db12402d8040dd
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82835996"
---
# <a name="change-settings-on-your-azure-stack-hub-switch-configuration"></a>更改 Azure Stack 集线器交换机配置的设置

可以更改 Azure Stack 集线器交换机配置的一些环境设置。 可以在原始设备制造商 (OEM) 创建的模板中确定哪些设置可以更改。 本文介绍其中的每个可自定义设置，以及更改会如何影响 Azure Stack 中心。 这些设置包括密码更新、syslog 服务器、简单网络管理协议（SNMP）监视、身份验证和访问控制列表。

在 Azure Stack 中心解决方案的部署过程中，原始设备制造商（OEM）会为 T) 和 BMC 创建和应用交换机配置。 OEM 使用 Azure Stack 集线器自动化工具来验证是否在这些设备上正确设置了所需的配置。 此配置基于 Azure Stack 中心[部署工作表](azure-stack-deployment-worksheet.md)中的信息。

> [!Warning]  
> OEM 创建配置后，请**不要**在不同意 OEM 或 Microsoft Azure Stack 中心工程团队同意的情况下更改配置。 对网络设备配置的更改会显著影响 Azure Stack 中心实例中的网络问题的操作或故障排除。
>
> 有关网络设备上这些功能的详细信息，如何进行这些更改，请联系 OEM 硬件提供商或 Microsoft 支持部门。 你的 OEM 具有基于 Azure Stack 中心部署工作表的自动化工具创建的配置文件。

但是，网络交换机的配置上的某些值是可以添加、删除或更改的。

## <a name="password-update"></a>密码更新

操作员随时可以更新网络交换机上任何用户的密码。 不需要更改 Azure Stack 中心系统上的任何信息，也不需要使用[Azure Stack 中心的轮换机密](azure-stack-rotate-secrets.md)步骤。

## <a name="syslog-server"></a>Syslog 服务器

操作员可以将交换机日志重定向到其数据中心的 Syslog 服务器。 使用此配置可确保特定时间点的日志可用于故障排除。 默认情况下，日志存储在交换机上，但其存储日志的容量有限。 查看[访问控制列表更新](#access-control-list-updates)部分，了解有关如何配置交换机管理访问权限的概述。

## <a name="snmp-monitoring"></a>SNMP 监视

操作员可以配置 SNMP v2 或 v3 来监视网络设备，并将陷阱发送到数据中心的网络监视应用。 出于安全原因，请使用 SNMPv3，因为它比 v2 更安全。 对于所需的 MIB 和配置，请咨询 OEM 硬件提供商。 查看[访问控制列表更新](#access-control-list-updates)部分，了解有关如何配置交换机管理访问权限的概述。

## <a name="authentication"></a>身份验证

操作员可以配置 RADIUS 或 TACACS，以便管理网络设备上的身份验证。 对于所需的受支持的方法和配置，请咨询 OEM 硬件提供商。 请查看[访问控制列表更新](#access-control-list-updates)部分，大致了解如何配置进行交换机管理访问所需的权限。

## <a name="access-control-list-updates"></a>访问控制列表更新

> [!NOTE]
> 从 1910 版开始，部署工作表将为**允许的网络**添加一个新字段，取代允许从受信任的数据中心网络范围访问网络设备管理接口和硬件生命周期主机 (HLH) 所需的手动步骤。 有关此新功能的详细信息，请参阅[Azure Stack 中心规划网络集成](azure-stack-network.md#permitted-networks)。

操作员可以更改某些访问控制列表（ACL），以允许来自受信任的数据中心网络范围的网络设备管理接口和硬件生命周期主机（HLH）的访问。 使用访问控制列表，操作员可以在特定网络范围内允许其管理 jumpbox Vm 访问交换机管理接口、HLH OS 和 HLH BMC。

## <a name="next-steps"></a>后续步骤

[Azure Stack 中心数据中心集成-DNS](azure-stack-integrate-dns.md)
