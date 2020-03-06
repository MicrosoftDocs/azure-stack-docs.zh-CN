---
title: 配置 Azure Stack 中心安全控制
description: 了解如何在 Azure Stack 中心配置安全控制
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 4cfc5ee7f9e2284b3f7f5c632980235b24d2e956
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367499"
---
# <a name="configure-azure-stack-hub-security-controls"></a>配置 Azure Stack 中心安全控制

本文介绍可在 Azure Stack 中心更改的安全控制，并在适用情况下突出显示利弊。

Azure Stack 中心体系结构建立在两个安全原则上：默认情况下，假定违反和强制执行。 Azure Stack 中心安全性的详细信息，请参阅[Azure Stack 中心基础结构安全状况](azure-stack-security-foundations.md)。 尽管 Azure Stack 集线器的默认安全状态是生产就绪，但还是存在一些需要额外强化的部署方案。

## <a name="tls-version-policy"></a>TLS 版本策略

传输层安全性（TLS）协议是一种广泛采用的加密协议，通过网络建立加密的通信。 TLS 随着时间的推移而不断发展，并已发布多个版本。 对于所有通信，Azure Stack 集线器基础结构仅使用 TLS 1.2。 对于外部接口，Azure Stack 中心目前默认为使用 TLS 1.2。 但是，为了向后兼容，它还支持向下协商到 TLS 1.1。 和1.0。 当 TLS 客户端请求通过 TLS 1.1 或 TLS 1.0 进行通信时，Azure Stack 集线器会通过协商到较低的 TLS 版本来接受请求。 如果客户端请求 TLS 1.2，Azure Stack 集线器将使用 TLS 1.2 建立 TLS 连接。

由于 TLS 1.0 和1.1 是以增量方式弃用或禁止的，从1906更新开始，你现在可以在 Azure Stack 中心配置 TLS 策略。 你可以强制实施仅限 TLS 1.2 的策略，在这种情况下，不允许和拒绝建立版本低于1.2 的 TLS 会话的任何尝试。

> [!IMPORTANT]
> Microsoft 建议对 Azure Stack 中心生产环境使用仅限 TLS 1.2 的策略。

## <a name="get-tls-policy"></a>获取 TLS 策略

使用[特权终结点（PEP）](azure-stack-privileged-endpoint.md)查看所有 Azure Stack 中心终结点的 TLS 策略：

```powershell
Get-TLSPolicy
```

示例输出：

    TLS_1.2

## <a name="set-tls-policy"></a>设置 TLS 策略

使用[特权终结点（PEP）](azure-stack-privileged-endpoint.md)为所有 Azure Stack 中心终结点设置 TLS 策略：

```powershell
Set-TLSPolicy -Version <String>
```

*TLSPolicy* cmdlet 的参数：

| 参数 | 说明 | 类型 | 必选 |
|---------|---------|---------|---------|
| *版本* | Azure Stack 集线器中允许的 TLS 版本 | String | 是|

使用以下值之一为所有 Azure Stack 中心终结点配置允许的 TLS 版本：

| 版本值 | 说明 |
|---------|---------|
| *TLS_All* | Azure Stack 集线器 TLS 终结点支持 TLS 1.2，但允许向 TLS 1.1 和 TLS 1.0 进行向下协商。 |
| *TLS_1 2* | Azure Stack 集线器 TLS 终结点仅支持 TLS 1.2。 | 

更新 TLS 策略需要几分钟才能完成。

### <a name="enforce-tls-12-configuration-example"></a>强制执行 TLS 1.2 配置示例

此示例将 TLS 策略设置为仅强制实施 TLS 1.2。

```powershell
Set-TLSPolicy -Version TLS_1.2
```

示例输出：

    VERBOSE: Successfully setting enforce TLS 1.2 to True
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is enforced

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>允许 TLS 的所有版本（1.2、1.1 和1.0）配置示例

此示例将 TLS 策略设置为允许 TLS 的所有版本（1.2、1.1 和1.0）。

```powershell
Set-TLSPolicy -Version TLS_All
```

示例输出：

    VERBOSE: Successfully setting enforce TLS 1.2 to False
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is not enforced

## <a name="next-steps"></a>后续步骤

- [了解 Azure Stack 集线器基础结构安全状况](azure-stack-security-foundations.md)
- [了解如何在 Azure Stack 中心旋转机密](azure-stack-rotate-secrets.md)
- [更新 Azure Stack 集线器上的 Windows Defender 防病毒](azure-stack-security-av.md)
