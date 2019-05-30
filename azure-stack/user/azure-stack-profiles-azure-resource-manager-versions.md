---
title: Azure Stack 中的配置文件支持的资源提供程序 API 版本 | Microsoft Docs
description: 了解如何在 Azure Stack 中的配置文件支持的 Azure 资源管理器 API 版本。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 21e1e8df3d5f43f91e391b8c39f3f5aca3aefea8
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269573"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Azure Stack 中的配置文件支持的资源提供程序 API 版本

可以在本文中找到 Azure Stack 使用的每个 API 配置文件的资源提供程序和版本号。 本文中的表格列出了每个资源提供程序支持的版本以及配置文件的 API 版本。 每个资源提供程序包含一组资源类型和特定的版本号。

API 配置文件使用三个命名约定：

- **latest**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

有关 Azure Stack 的 API 配置文件和版本发布频率的说明，请参阅[在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md)。

> [!Note]
> **最新** API 配置文件包含最新版资源提供程序 API，未在本文中列出。

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>2019-03-01-混合配置文件的概述

| 资源提供程序 | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN 网关将为 2017年-10-01 |
| Microsoft.Storage（数据平面） | 2017-10-01 |
| Microsoft.Storage（控制平面） | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01（未更改） |
| 对 Microsoft.Resources （Azure 资源管理器本身） | 2016-06-01 |
| Microsoft.Authorization （策略操作） | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

API 配置文件中的提供程序每种资源类型的版本的列表，请参阅[2019年-03-01-混合配置文件的详细信息](#details-for-the-2019-03-01-hybrid-profile)。

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>2019-03-01-混合配置文件的详细信息

### <a name="microsoftauthorization"></a>Microsoft.Authorization

基于角色的访问控制可让你管理你的组织中的用户可对资源执行的操作。 可以定义角色、 将角色分配给用户或组，并获取有关权限的信息。 有关详细信息，请参阅[授权](/rest/api/authorization/)。

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
| Locations/vmSizes | 2017-12-01 |
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
| 度量值 | 2018-01-01 |
| 诊断设置 | 2017-05-01-preview |
| 诊断设置类别 | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

管理密钥保管库，以及密钥、 机密和密钥保管库中的证书。 有关详细信息，请参阅 [Azure Key Vault REST API reference](/rest/api/keyvault/)（Azure Key Vault REST API 参考）。

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
| Location/operationResults | 2017-10-01 |
| 位置/操作 | 2017-10-01 |
| 位置/使用情况 |2017-10-01 |
| 网络接口 | 2017-10-01 |
| 网络安全组 | 2017-10-01 |
| 操作 | 2017-10-01 |
| 公用 IP 地址 | 2017-10-01 |
| 路由表 | 2017-10-01 |
| 虚拟网络网关 | 2017-10-01 |
| 虚拟网络 | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure 资源管理器可让你部署和管理 Azure 解决方案的基础结构。 资源组中组织相关的资源和部署使用 JSON 模板资源。 有关使用资源管理器部署和管理资源的简介，请参阅 [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。

| 资源类型 | API 版本 |
|-----------------------------------------|-------------------|
| 部署 | 2018-05-01 |
| 部署/操作 | 2018-05-01 |
| 链接 | 2018-05-01 |
| 位置 | 2018-05-01 |
| 操作 | 2018-05-01 |
| 提供程序 | 2018-05-01 |
| 资源组| 2018-05-01 |
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

存储资源提供程序 (SRP) 允许你以编程方式管理你的存储帐户和密钥。 有关详细信息，请参阅 [Azure Storage Resource Provider REST API reference](/rest/api/storagerp/)（Azure 存储资源提供程序 REST API 参考）。

| 资源类型 | API 版本 |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| 位置 | 2017-10-01 |
| 位置/配额 | 2017-10-01 |
| 操作 | 2017-10-01 |
| 存储帐户 | 2017-10-01 |
| 使用情况 | 2017-10-01 |

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
