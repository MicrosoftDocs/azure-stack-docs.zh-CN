---
title: Azure Stack Hub 中的配置文件支持的资源提供程序 API 版本
description: 了解 Azure Stack Hub 中的配置文件支持的 Azure 资源管理器 API 版本。
author: sethmanheim
ms.custom: contperfq4
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 5b558eb0abb0494cb75f2509e51f291b08bd29ef
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525534"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack-hub"></a>Azure Stack Hub 中的配置文件支持的资源提供程序 API 版本

可以在本文中找到 Azure Stack Hub 使用的每个 API 配置文件的资源提供程序和版本号。 本文中的表格列出了每个资源提供程序支持的版本以及配置文件的 API 版本。 每个资源提供程序包含一组资源类型和特定的版本号。

API 配置文件使用三个命名约定：

- **latest**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

有关 Azure Stack Hub 的 API 配置文件和版本发布频率的说明，请参阅[在 Azure Stack Hub 中管理 API 版本配置文件](azure-stack-version-profiles.md)。

> [!NOTE]
> **最新** API 配置文件包含最新版资源提供程序 API，未在本文中列出。

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>2019-03-01-hybrid 配置文件概述

| 资源提供程序 | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN 网关将为 2017-10-01 |
| Microsoft.Storage（数据平面） | 2017-11-09 |
| Microsoft.Storage（控制平面） | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01（未更改） |
| Microsoft.Resources（Azure 资源管理器本身） | 2016-06-01 |
| Microsoft.Authorization（策略操作） | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

有关 API 配置文件中提供程序的每种资源类型的版本列表，请参阅 [2019-03-01-hybrid 配置文件的详细信息](#details-for-the-2019-03-01-hybrid-profile)。

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>2019-03-01-hybrid 配置文件的详细信息

### <a name="microsoftauthorization"></a>Microsoft.Authorization

基于角色的访问控制可让你管理组织中用户可对资源执行的操作。 你可以定义角色、将角色分配给用户或组，以及获取有关权限的信息。 有关详细信息，请参阅[授权](/rest/api/authorization/)。

| 资源类型 | API 版本 |
|---------------------|--------------------|
| 锁 | 2016-09-01 |
| 操作 | 2015-07-01 |
| 权限 | 2015-07-01 |
| 策略分配 | 2016-12-01 |
| 策略定义 | 2016-12-01 |
| 提供程序操作 | 2015-07-01 |
| 角色分配 | 2015-07-01 |
| 角色定义 | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| 资源类型 | API 版本 |
|----------------------------------|----------------------|
| 委托的提供程序订阅 | 2015-06-01 - preview |
| 委托的使用情况聚合 | 2015-06-01 - preview |
| 估算资源开支 | 2015-06-01 - preview |
| 操作 | 2015-06-01 - preview |
| 订阅方使用情况聚合 | 2015-06-01 - preview |
| 使用情况聚合 | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

使用 Azure 计算 API 可通过编程方式访问虚拟机及其支持资源。 有关详细信息，请参阅 [Azure 计算](/rest/api/compute/)。

| 资源类型 | API 版本 |
|---------------------------------------------------------------|-------------|
| 可用性集 | 2017-12-01 |
| 位置 | 2017-12-01 |
| 位置/操作 | 2017-12-01 |
| 位置/发布者 | 2017-12-01 |
| 位置/使用情况 | 2017-12-01 |
| 位置/VM 大小 | 2017-12-01 |
| 操作 | 2017-12-01 |
| 虚拟机 | 2017-12-01 |
| 虚拟机/扩展 | 2017-12-01 |
| 虚拟机规模集 | 2017-12-01 |
| 虚拟机规模集/扩展 | 2017-12-01 |
| 虚拟机规模集/网络接口 | 2017-12-01 |
| 虚拟机规模集/虚拟机 | 2017-12-01|
| 虚拟机规模集/虚拟机/网络接口 | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| 资源类型 | API 版本 |
|------------------|-------------|
| 特选 | 2015-04-01 |
| 特选内容 | 2015-04-01 |
| 特选摘录 | 2015-04-01 |
| 库项 | 2015-04-01 |
| 操作 | 2015-04-01 |
| 门户 | 2015-04-01 |
| 搜索 | 2015-04-01 |
| 建议 | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| 资源类型 | API 版本 |
|--------------------|--------------------|
| 操作 | 2015-04-01 |
| 事件类型 | 2015-04-01 |
| 事件类别 | 2015-04-01 |
| 指标定义 | 2018-01-01 |
| 指标 | 2018-01-01 |
| 诊断设置 | 2017-05-01-preview |
| 诊断设置类别 | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

管理 Key Vault，以及 Key Vault 中的密钥、机密和证书。 有关详细信息，请参阅 [Azure Key Vault REST API reference](/rest/api/keyvault/)（Azure Key Vault REST API 参考）。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| 操作 | 2016-10-01 |
| 保管库 | 2016-10-01 |
| 保管库/访问策略 | 2016-10-01 |
| 保管库/机密 | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

操作调用结果是可用网络云操作列表的表示形式。 有关详细信息，请参阅[操作 REST API](/rest/api/operation/)。

| 资源类型 | API 版本 |
|---------------------------|--------------|
| 连接 | 2017-10-01 |
| DNS 区域 | 2016-04-01 |
| 负载均衡器 | 2017-10-01 |
| 本地网络网关 | 2017-10-01 |
| 位置 | 2017-10-01|
| 位置/操作结果 | 2017-10-01 |
| 位置/操作 | 2017-10-01 |
| 位置/使用情况 |2017-10-01 |
| 网络接口 | 2017-10-01 |
| 网络安全组 | 2017-10-01 |
| 操作 | 2017-10-01 |
| 公共 IP 地址 | 2017-10-01 |
| 路由表 | 2017-10-01 |
| 虚拟网络网关 | 2017-10-01 |
| 虚拟网络 | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

使用 Azure 资源管理器可以部署和管理 Azure 解决方案的基础结构。 可在资源组中组织相关的资源，并使用 JSON 模板部署资源。 有关使用资源管理器部署和管理资源的简介，请参阅 [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。

| 资源类型 | API 版本 |
|-----------------------------------------|-------------------|
| 部署 | 2018-05-01 |
| 部署/操作 | 2018-05-01 |
| 链接 | 2018-05-01 |
| 位置 | 2018-05-01 |
| 操作 | 2018-05-01 |
| 提供程序 | 2018-05-01 |
| ResourceGroups| 2018-05-01 |
| 资源 | 2018-05-01 |
| 订阅 | 2018-05-01 |
| 订阅/位置 | 2016-06-01 |
| 订阅/操作结果 | 2018-05-01 |
| 订阅/提供程序 | 2018-05-01 |
| 订阅/资源组 | 2018-05-01 |
| 订阅/资源组/资源 | 2018-05-01 |
| 订阅/资源 | 2018-05-01 |
| 订阅/标记名称 | 2018-05-01 |
| 订阅/标记名称/标记值 | 2018-05-01 |
| 租户 | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

使用存储资源提供程序 (SRP) 可通过编程方式管理存储帐户和密钥。 有关详细信息，请参阅 [Azure Storage Resource Provider REST API reference](/rest/api/storagerp/)（Azure 存储资源提供程序 REST API 参考）。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| 位置 | 2017-10-01 |
| 位置/配额 | 2017-10-01 |
| 操作 | 2017-10-01 |
| 存储帐户 | 2017-10-01 |
| 用途 | 2017-10-01 |

## <a name="next-steps"></a>后续步骤

- [安装适用于 Azure Stack Hub 的 PowerShell](../operator/powershell-install-az-module.md)
- [配置 Azure Stack Hub PowerShell 环境](azure-stack-powershell-configure-user.md)  
