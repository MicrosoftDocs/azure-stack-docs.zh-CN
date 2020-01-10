---
title: 在 Azure Stack 集线器交换机配置中修改特定设置 |Microsoft Docs
description: 了解可在 Azure Stack 集线器交换机配置中自定义的内容。 原始设备制造商（OEM）创建配置后，请不要在不同意 OEM 或 Microsoft Azure Stack 中心工程团队同意的情况下进行更改。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: Femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 730abce4712530c257ebde332ce220fe14cea772
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811917"
---
#  <a name="modify-specific-settings-on-your-azure-stack-hub-switch-configuration"></a>在 Azure Stack 集线器交换机配置上修改特定设置

你可以为 Azure Stack 集线器交换机配置修改一些环境设置。 可以在原始设备制造商（OEM）创建的模板中确定可以更改的设置。 本文介绍其中的每个可自定义设置，以及更改会如何影响 Azure Stack 中心。 这些设置包括密码更新、syslog 服务器、SNMP 监视、身份验证和访问控制列表。 

在 Azure Stack 中心解决方案的部署过程中，原始设备制造商（OEM）会为 T) 和 BMC 创建和应用交换机配置。 OEM 使用 Azure Stack 集线器自动化工具来验证是否在这些设备上正确设置了所需的配置。 此配置基于 Azure Stack 中心[部署工作表](azure-stack-deployment-worksheet.md)中的信息。 OEM 创建配置后，请**不要**在不同意 OEM 或 Microsoft Azure Stack 中心工程团队同意的情况下更改配置。 对网络设备配置的更改会显著影响 Azure Stack 中心实例中的网络问题的操作或故障排除。

但是，可以在网络交换机配置中添加、删除或更改某些值。

>[!Warning]  
> **请勿**在 OEM 或 Microsoft Azure Stack 中心工程团队同意的情况下更改配置。 对网络设备配置的更改会显著影响 Azure Stack 中心实例中的网络问题的操作或故障排除。
>
> 有关网络设备上这些功能的详细信息，如何进行这些更改，请联系 OEM 硬件提供商或 Microsoft 支持部门。 你的 OEM 具有基于 Azure Stack 中心部署工作表的自动化工具创建的配置文件。 

## <a name="password-update"></a>密码更新

操作员随时可以更新网络交换机上任何用户的密码。 不需要更改 Azure Stack 中心系统上的任何信息，也不需要使用[Azure Stack 中心的轮换机密](azure-stack-rotate-secrets.md)步骤。

## <a name="syslog-server"></a>Syslog 服务器

操作员可以将交换机日志重定向到其数据中心的 syslog 服务器。 使用此配置可确保特定时间点的日志可用于故障排除。 默认情况下，日志存储在交换机上;它们存储日志的容量有限。 查看[访问控制列表更新](#access-control-list-updates)部分，了解有关如何配置交换机管理访问权限的概述。

## <a name="snmp-monitoring"></a>SNMP 监视

操作员可以配置简单网络管理协议（SNMP） v2 或 v3 来监视网络设备并将陷阱发送到数据中心的网络监视应用程序。 出于安全原因，请使用 SNMPv3，因为它比 v2 更安全。 请咨询 OEM 硬件提供商以获取 Mib 和所需的配置。 查看[访问控制列表更新](#access-control-list-updates)部分，了解有关如何配置交换机管理访问权限的概述。

## <a name="authentication"></a>身份验证

操作员可以配置 RADIUS 或 TACACS 来管理网络设备上的身份验证。 请咨询 OEM 硬件提供商，以获取所需的支持方法和配置。  查看[访问控制列表更新](#access-control-list-updates)部分，了解有关如何配置交换机管理访问权限的概述。

## <a name="access-control-list-updates"></a>访问控制列表更新

> [!NOTE]
> 从1910开始，部署工作表将为**允许的网络**提供一个新字段，该字段将替代允许从受信任的数据中心网络范围访问网络设备管理接口和硬件生命周期主机（HLH）所需的手动步骤。 有关此新功能的详细信息，请查看[Azure Stack 集线器的网络集成规划](azure-stack-network.md#permitted-networks)。

操作员可以更改某些访问控制列表（ACL），以允许来自受信任的数据中心网络范围的网络设备管理接口和硬件生命周期主机（HLH）的访问。 使用访问控制列表，操作员可以在特定网络范围内允许其管理 jumpbox Vm 访问交换机管理接口、HLH OS 和 HLH BMC。

## <a name="next-steps"></a>后续步骤

[Azure Stack 中心数据中心集成-DNS](azure-stack-integrate-dns.md)
