---
title: 修改 Azure Stack Hub 交换机配置上的特定设置
description: 了解可以在 Azure Stack Hub 交换机配置上进行哪些自定义。 在原始设备制造商 (OEM) 创建配置以后，请勿在没有获得 OEM 或 Microsoft Azure Stack Hub 工程团队同意的情况下更改它。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 19964b761478bb210d3377b177f05f708697167b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "78368713"
---
#  <a name="modify-specific-settings-on-your-azure-stack-hub-switch-configuration"></a>修改 Azure Stack Hub 交换机配置上的特定设置

可以修改 Azure Stack Hub 交换机配置的一些环境设置。 可以在原始设备制造商 (OEM) 创建的模板中确定哪些设置可以更改。 本文介绍每项这样的可自定义设置，以及所做的更改对 Azure Stack Hub 的具体影响。 这些设置包括密码更新、Syslog 服务器、SNMP 监视、身份验证，以及访问控制列表。 

在部署 Azure Stack Hub 解决方案期间，原始设备制造商 (OEM) 会为 TOR 和 BMC 创建并应用交换机配置。 OEM 使用 Azure Stack Hub 自动化工具来验证所需的配置是否已在这些设备上正确设置。 配置基于 Azure Stack Hub [部署工作表](azure-stack-deployment-worksheet.md)中的信息。 OEM 创建配置后，请**不要**在不同意 OEM 或 Microsoft Azure Stack 中心工程团队同意的情况下更改配置。 更改网络设备配置可能会显著影响 Azure Stack Hub 实例中网络问题的操作或排查。

但是，网络交换机的配置上的某些值是可以添加、删除或更改的。

>[!Warning]  
> **请勿**在 OEM 或 Microsoft Azure Stack 中心工程团队同意的情况下更改配置。 更改网络设备配置可能会显著影响 Azure Stack Hub 实例中网络问题的操作或排查。
>
> 有关网络设备上这些功能的详细信息，如何进行这些更改，请联系 OEM 硬件提供商或 Microsoft 支持部门。 OEM 根据你的 Azure Stack Hub 部署工作表通过自动化工具创建配置文件。 

## <a name="password-update"></a>密码更新

操作员可以随时为网络交换机上的任何用户更新密码。 不需更改 Azure Stack Hub 系统上的任何信息，也不需使用[在 Azure Stack Hub 中轮换机密](azure-stack-rotate-secrets.md)所需的步骤。

## <a name="syslog-server"></a>Syslog 服务器

操作员可以将交换机日志重定向到其数据中心的 Syslog 服务器。 使用此配置可确保特定时间点的日志可以用来进行故障排除。 默认情况下，日志存储在交换机上；交换机用于存储日志的容量有限。 请查看[访问控制列表更新](#access-control-list-updates)部分，大致了解如何配置进行交换机管理访问所需的权限。

## <a name="snmp-monitoring"></a>SNMP 监视

操作员可以配置简单网络管理协议 (SNMP) v2 或 v3，以便监视网络设备并向数据中心的网络监视应用程序发送陷阱。 出于安全考虑，请使用 SNMPv3，因为它比 v2 更安全。 对于所需的 MIB 和配置，请咨询 OEM 硬件提供商。 请查看[访问控制列表更新](#access-control-list-updates)部分，大致了解如何配置进行交换机管理访问所需的权限。

## <a name="authentication"></a>Authentication

操作员可以配置 RADIUS 或 TACACS，以便管理网络设备上的身份验证。 对于所需的受支持的方法和配置，请咨询 OEM 硬件提供商。  请查看[访问控制列表更新](#access-control-list-updates)部分，大致了解如何配置进行交换机管理访问所需的权限。

## <a name="access-control-list-updates"></a>访问控制列表更新

> [!NOTE]
> 从 1910 版开始，部署工作表将为**允许的网络**添加一个新字段，取代允许从受信任的数据中心网络范围访问网络设备管理接口和硬件生命周期主机 (HLH) 所需的手动步骤。 有关此新功能的详细信息，请查看 [Azure Stack Hub 的网络集成规划](azure-stack-network.md#permitted-networks)。

操作员可以更改某些访问控制列表 (ACL)，允许从一系列受信任的数据中心网络访问网络设备管理接口和硬件生命周期主机 (HLH)。 操作员可以通过访问控制列表允许特定网络范围内的管理 jumpbox VM 访问交换机管理接口、HLH OS 和 HLH BMC。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 数据中心集成 - DNS](azure-stack-integrate-dns.md)
