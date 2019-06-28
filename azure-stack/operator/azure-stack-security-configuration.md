---
title: 配置 Azure Stack 的安全控制
description: 了解如何在 Azure Stack 中配置安全控制
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: c82ba5ca0de27ece5d3bf0ef260efa8d182ad9bf
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419765"
---
# <a name="configure-azure-stack-security-controls"></a>配置 Azure Stack 的安全控制

*适用于：Azure Stack 集成系统*

本文介绍了可以在 Azure Stack 中更改并突出显示的权衡，在适用的情况的安全控件。

Azure Stack 体系结构基于两个安全原则支柱： 假设性违规和默认强化。 Azure Stack 安全性的详细信息，请参阅[Azure Stack 基础结构安全局势](azure-stack-security-foundations.md)。 虽然 Azure Stack 的默认安全状况可用于生产环境，有一些需要其他强化的部署方案。

## <a name="tls-version-policy"></a>TLS 版本策略

传输层安全性 (TLS) 协议是一个广泛采用的加密协议通过网络建立加密的通信。 TLS 的不断发展变化，并且已发布多个版本。 Azure Stack 基础结构以独占方式使用 TLS 1.2 来确保所有通信。 对于外部接口，Azure Stack 当前默认为使用 TLS 1.2。 但是，对于向后兼容性，它还支持 TLS 1.1 下协商。 和 1.0。 当 TLS 客户端请求通过 TLS 1.1 或 TLS 1.0 进行通信时，Azure Stack 允许该请求但协商到更低 TLS 版本。 如果客户端请求 TLS 1.2，Azure Stack 将建立 TLS 连接使用 TLS 1.2。

因为 TLS 1.0 和 1.1 版以增量方式被弃用或禁止组织和符合性标准，从 1906年更新开始，你现在可以配置 TLS 策略在 Azure Stack 中。 您可以强制执行 TLS 1.2 唯一策略其中建立与版本低于 1.2 的 TLS 会话的任何尝试进行不允许和拒绝。

> [!IMPORTANT]
> 使用 TLS 1.2 策略仅适用于生产环境。

## <a name="get-tls-policy"></a>获取 TLS 策略

使用[特权终结点 (PEP)](azure-stack-privileged-endpoint.md)若要查看所有 Azure Stack 终结点的 TLS 策略：

```powershell
Get-TLSPolicy
```

示例输出：

    TLS_1.2

## <a name="set-tls-policy"></a>设置 TLS 策略

使用[特权终结点 (PEP)](azure-stack-privileged-endpoint.md)设置 TLS 策略的所有 Azure Stack 终结点：

```powershell
Set-TLSPolicy -Version <String>
```

为参数*集 TLSPolicy* cmdlet:

| 参数 | 描述 | Type | 需要 |
|---------|---------|---------|---------|
| *版本* | 在 Azure Stack 中允许的 TLS 版本信息 | String | 是|

使用以下值之一来配置所有 Azure Stack 终结点允许的 TLS 版本：

| 版本值 | 描述 |
|---------|---------|
| *TLS_All* | Azure Stack TLS 终结点支持 TLS 1.2，但向下协商为 TLS 1.1 和 TLS 1.0 允许。 |
| *TLS_1.2* | Azure Stack TLS 终结点仅支持 TLS 1.2。 | 

正在更新 TLS 策略需要几分钟才能完成。

### <a name="enforce-tls-12-configuration-example"></a>强制实施 TLS 1.2 的配置示例

此示例设置 TLS 策略以仅强制实施 TLS 1.2。

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

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>允许所有版本的 TLS （1.2、 1.1 和 1.0） 的配置示例

此示例设置 TLS 策略以允许 TLS （1.2、 1.1 和 1.0） 的所有版本。

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

- [了解 Azure Stack 基础结构安全局势](azure-stack-security-foundations.md)
- [了解如何在 Azure Stack 中轮换机密](azure-stack-rotate-secrets.md)
- [更新 Azure Stack 上的 Windows Defender 防病毒](azure-stack-security-av.md)