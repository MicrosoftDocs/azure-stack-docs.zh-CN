---
title: 在 Azure Stack Hub 中配置安全控制
description: 了解如何在 Azure Stack Hub 中配置安全控制。
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 2ba13915c572c4b71cd5261ddb08cb876dd137a5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869247"
---
# <a name="configure-azure-stack-hub-security-controls"></a>配置 Azure Stack Hub 安全控制

本文介绍可在 Azure Stack Hub 中更改的安全控制，并强调如何在适当的情况下进行取舍。

Azure Stack Hub 体系结构构建在两大安全原则支柱之上：假想违规和默认强化。 有关 Azure Stack Hub 安全性的详细信息，请参阅 [Azure Stack Hub 基础结构安全态势](azure-stack-security-foundations.md)。 当 Azure Stack Hub 的默认安全局势适合生产环境时，仍有一些部署方案需要额外的强化。

## <a name="tls-version-policy"></a>TLS 版本策略

传输层安全性 (TLS) 协议是广为采用的加密协议，可通过网络建立加密的通信。 TLS 的发展已有一段时间，并已发布多个版本。 Azure Stack Hub 基础结构对其所有通信专门使用 TLS 1.2。 对于外部接口，Azure Stack Hub 目前默认使用 TLS 1.2。 但是，为了实现后向兼容，它也支持向下协商到 TLS 1.1 和 1.0。 当 TLS 客户端请求通过 TLS 1.1 或 TLS 1.0 通信时，Azure Stack Hub 将通过与更低的 TLS 版本协商来接受请求。 如果客户端请求 TLS 1.2，Azure Stack Hub 将使用 TLS 1.2 建立 TLS 连接。

由于 TLS 1.0 和 1.1 版正逐渐由于组织和合规性标准而淘汰或禁用，从 1906 更新开始，你可以在 Azure Stack Hub 中配置 TLS 策略。 可以强制使用仅限 TLS 1.2 的策略，使用低于 1.2 的版本尝试建立任何 TLS 会话都将被禁止或拒绝。

> [!IMPORTANT]
> Microsoft 建议对 Azure Stack 中心生产环境使用仅限 TLS 1.2 的策略。

## <a name="get-tls-policy"></a>获取 TLS 策略

使用[特权终结点 (PEP)](azure-stack-privileged-endpoint.md) 查看所有 Azure Stack Hub 终结点的 TLS 策略：

```powershell
Get-TLSPolicy
```

示例输出：

```powershell
TLS_1.2
```

## <a name="set-tls-policy"></a>设置 TLS 策略

使用[特权终结点 (PEP)](azure-stack-privileged-endpoint.md) 设置所有 Azure Stack Hub 终结点的 TLS 策略：

```powershell
Set-TLSPolicy -Version <String>
```

*Set-TLSPolicy* cmdlet 的参数：

| 参数 | 说明 | 类型 | 必须 |
|-----|-----|-----|-----|
| *版本* | Azure Stack Hub 中允许的 TLS 版本 | String | 是 |

使用以下值之一为所有 Azure Stack Hub 终结点配置允许的 TLS 版本：

| 版本值 | 说明 |
|-------|-------|
| *TLS_All* | Azure Stack Hub TLS 终结点支持 TLS 1.2，但可向下协商到 TLS 1.1 和 TLS 1.0。 |
| *TLS_1.2* | Azure Stack Hub TLS 终结点仅支持 TLS 1.2。 |

完成 TLS 策略更新需要几分钟的时间。

### <a name="enforce-tls-12-configuration-example"></a>强制实施 TLS 1.2 配置的示例

本示例将 TLS 策略设置为仅强制实施 TLS 1.2。

```powershell
Set-TLSPolicy -Version TLS_1.2
```

示例输出：

```powershell
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
```

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>允许所有 TLS 版本（1.2、1.1 和 1.0）的配置示例

本示例将 TLS 策略设置为允许所有 TLS 版本（1.2、1.1 和 1.0）。

```powershell
Set-TLSPolicy -Version TLS_All
```

示例输出：

```powershell
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
```

## <a name="next-steps"></a>后续步骤

- [了解 Azure Stack Hub 基础结构安全态势](azure-stack-security-foundations.md)。
- [了解如何在 Azure Stack Hub 中轮换机密](azure-stack-rotate-secrets.md)。
- [在 Azure Stack Hub 上更新 Windows Defender 防病毒](azure-stack-security-av.md)。
