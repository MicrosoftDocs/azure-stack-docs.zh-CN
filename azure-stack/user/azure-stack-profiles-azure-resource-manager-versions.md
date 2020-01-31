---
title: Azure Stack 集线器中的配置文件支持的资源提供程序 API 版本
description: 了解 Azure Stack 中心中的配置文件支持的 Azure 资源管理器 API 版本。
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 03ec8b455991a85515793d1098df0f72497299b9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883302"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack-hub"></a>Azure Stack 集线器中的配置文件支持的资源提供程序 API 版本

可以在本文中找到 Azure Stack 集线器使用的每个 API 配置文件的资源提供程序和版本号。 本文中的表列出了每个资源提供程序支持的版本以及配置文件的 API 版本。 每个资源提供程序都包含一组资源类型和特定的版本号。

API 配置文件使用三个命名约定：

- **最近**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

有关 Azure Stack 中心的 API 配置文件和版本发布节奏的说明，请参阅[在 Azure Stack 中心管理 api 版本配置文件](azure-stack-version-profiles.md)。

> [!NOTE]
> **最新**的 API 配置文件包含资源提供程序 API 的最新版本，但未在本文中列出。

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>2019-03-01-混合配置文件概述

| 资源提供程序 | api 版本 |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN 网关将为2017-10-01 |
| Microsoft. 存储（数据平面） | 2017-11-09 |
| Microsoft. Storage （控制平面） | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 （未更改） |
| Microsoft .Resources （Azure 资源管理器自身） | 2016-06-01 |
| Microsoft. 授权（策略操作） | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

有关 API 配置文件中提供程序的每种资源类型的版本列表，请参阅[2019-03-01 混合配置文件的详细信息](#details-for-the-2019-03-01-hybrid-profile)。

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>2019-03-01 混合配置文件的详细信息

### <a name="microsoftauthorization"></a>Microsoft.Authorization

利用基于角色的访问控制，你可以管理组织中的用户可以对资源执行的操作。 你可以定义角色、将角色分配给用户或组，以及获取有关权限的信息。 有关详细信息，请参阅[授权](/rest/api/authorization/)。

| 资源类型 | API 版本 |
|---------------------|--------------------|
| 锁 | 2016-09-01 |
| Operations | 2015-07-01 |
| 权限 | 2015-07-01 |
| 策略分配 | 2016-12-01 |
| 策略定义 | 2016-12-01 |
| 提供程序操作 | 2015-07-01 |
| 角色分配 | 2015-07-01 |
| 角色定义 | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| 资源类型 | API 版本 |
|----------------------------------|----------------------|
| 委托的提供程序订阅 | 2015-06-01-预览 |
| 委托使用情况聚合 | 2015-06-01-预览 |
| 估计资源支出 | 2015-06-01-预览 |
| Operations | 2015-06-01-预览 |
| 订阅服务器使用情况聚合 | 2015-06-01-预览 |
| 使用情况聚合 | 2015-06-01-预览 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure 计算 Api 允许以编程方式访问虚拟机及其支持资源。 有关详细信息，请参阅[Azure 计算](/rest/api/compute/)。

| 资源类型 | API 版本 |
|---------------------------------------------------------------|-------------|
| 可用性集 | 2017-12-01 |
| 位置 | 2017-12-01 |
| 位置/操作 | 2017-12-01 |
| 位置/发布者 | 2017-12-01 |
| 位置/用法 | 2017-12-01 |
| Locations/vmSizes | 2017-12-01 |
| Operations | 2017-12-01 |
| 虚拟机 | 2017-12-01 |
| 虚拟机/扩展 | 2017-12-01 |
| 虚拟机规模集 | 2017-12-01 |
| 虚拟机规模集/扩展 | 2017-12-01 |
| 虚拟机规模集/网络接口 | 2017-12-01 |
| 虚拟机规模集/虚拟机 | 2017-12-01|
| 虚拟机规模集/virtualMachines/networkInterfaces | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| 资源类型 | API 版本 |
|------------------|-------------|
| 特选 | 2015-04-01 |
| 特选内容 | 2015-04-01 |
| 特选提取 | 2015-04-01 |
| 库项 | 2015-04-01 |
| Operations | 2015-04-01 |
| 门户 | 2015-04-01 |
| 搜索 | 2015-04-01 |
| 建议 | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| 资源类型 | API 版本 |
|--------------------|--------------------|
| Operations | 2015-04-01 |
| 活动类型 | 2015-04-01 |
| 事件类别 | 2015-04-01 |
| 指标定义 | 2018-01-01 |
| 指标 | 2018-01-01 |
| 诊断设置 | 2017-05-01-预览 |
| 诊断设置类别 | 2017-05-01-预览 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

管理你的 Key Vault 以及 Key Vault 中的密钥、机密和证书。 有关详细信息，请参阅[Azure Key Vault REST API 参考](/rest/api/keyvault/)。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| Operations | 2016-10-01 |
| 保管库 | 2016-10-01 |
| 保管库/访问策略 | 2016-10-01 |
| 保管库/机密 | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

操作调用结果是可用网络云操作列表的表示形式。 有关详细信息，请参阅[Operation REST API](/rest/api/operation/)。

| 资源类型 | API 版本 |
|---------------------------|--------------|
| 连接 | 2017-10-01 |
| DNS 区域 | 2016-04-01 |
| 负载均衡器 | 2017-10-01 |
| 本地网络网关 | 2017-10-01 |
| 位置 | 2017-10-01|
| Location/operationResults | 2017-10-01 |
| 位置/操作 | 2017-10-01 |
| 位置/用法 |2017-10-01 |
| 网络接口 | 2017-10-01 |
| 网络安全组 | 2017-10-01 |
| Operations | 2017-10-01 |
| 公共 IP 地址 | 2017-10-01 |
| 路由表 | 2017-10-01 |
| 虚拟网络网关 | 2017-10-01 |
| 虚拟网络 | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure 资源管理器使你可以部署和管理 Azure 解决方案的基础结构。 可在资源组中组织相关资源，并通过 JSON 模板部署资源。 有关使用资源管理器部署和管理资源的简介，请参阅[Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。

| 资源类型 | API 版本 |
|-----------------------------------------|-------------------|
| 部署 | 2018-05-01 |
| 部署/操作 | 2018-05-01 |
| 链接 | 2018-05-01 |
| 位置 | 2018-05-01 |
| Operations | 2018-05-01 |
| 提供商 | 2018-05-01 |
| ResourceGroups| 2018-05-01 |
| 资源 | 2018-05-01/ |
| 订阅 | 2018-05-01 |
| 订阅/位置 | 2016-06-01 |
| 订阅/operationresults | 2018-05-01 |
| 订阅/提供程序 | 2018-05-01 |
| Subscriptions/ResourceGroups | 2018-05-01 |
| Subscriptions/resourceGroups/resources | 2018-05-01 |
| 订阅/资源 | 2018-05-01 |
| Subscriptions/tagNames | 2018-05-01 |
| Subscriptions/tagNames/tagValues | 2018-05-01 |
| 租户 | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

存储资源提供程序（SRP）允许以编程方式管理存储帐户和密钥。 有关详细信息，请参阅[Azure 存储资源提供程序 REST API 参考](/rest/api/storagerp/)。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| 位置 | 2017-10-01 |
| 位置/配额 | 2017-10-01 |
| Operations | 2017-10-01 |
| StorageAccounts | 2017-10-01 |
| 用途 | 2017-10-01 |

## <a name="next-steps"></a>后续步骤

- [为 Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)
- [配置 Azure Stack 中心 PowerShell 环境](azure-stack-powershell-configure-user.md)  
