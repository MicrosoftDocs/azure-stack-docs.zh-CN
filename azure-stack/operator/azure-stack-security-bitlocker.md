---
title: Azure Stack 中的静态数据加密
description: 了解 Azure Stack 如何使用静态加密来保护数据
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 12/06/2018
ms.date: 01/14/2019
ms.author: v-jay
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 018b8f6cf4fc5d3cd380535fca71a038b7fd4208
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64291657"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Azure Stack 中的静态数据加密

Azure Stack 使用静态加密来保护存储子系统级别的用户数据和基础结构数据。 Azure Stack 的存储子系统是配合 128 位 AES 加密法使用 BitLocker 加密的。 BitLocker 密钥保存在内部机密存储中。

静态数据加密是许多主流合规标准（例如 PCI-DSS、FedRAMP、HIPAA）的共同要求。 Azure Stack 可你满足这些要求，而无需投入额外的工作或进行额外的配置。 有关 Azure Stack 如何帮助你满足合规要求的详细信息，请参阅 [Microsoft 服务信任门户](https://aka.ms/AzureStackCompliance)。

> [!NOTE]
> 静态数据加密可防止窃取一个或多个硬盘实物的人员访问你的数据。 当系统已启动且正在运行时，静态数据加密无法防止通过网络拦截数据（传输中的数据），也无法保护当前正在使用的数据（内存中的数据），或概括而言，无法防范数据外泄。

## <a name="retrieving-bitlocker-recovery-keys"></a>检索 BitLocker 恢复密钥

Azure Stack BitLocker 的静态数据密钥在内部管理。 对于日常操作或者在系统启动期间，无需提供这些密钥。 但是，支持方案可能要求提供 BitLocker 恢复密钥来让系统联机。  

> [!WARNING]
> 检索 BitLocker 恢复密钥，并将其存储在 Azure Stack 外部的安全位置。 在某些支持方案中，不提供恢复密钥可能会导致数据丢失，并需要从备份映像还原系统。

检索 BitLocker 恢复密钥需要访问[特权终结点](azure-stack-privileged-endpoint.md) (PEP)。 在 PEP 会话中运行 Get-AzsRecoveryKeys cmdlet。

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

*Get-AzsRecoveryKeys* cmdlet 的可选参数：

| 参数 | 描述 | Type | 需要 |
|---------|---------|---------|---------|
|*raw* | 返回每个加密卷的恢复密钥、计算机名与密码 ID 之间的映射原始数据  | 开关 | 否（专用于支持方案）|


## <a name="troubleshoot-issues"></a>排查问题

在极端情况下，BitLocker 解锁请求可能失败，并导致特定卷无法启动。 如果没有 BitLocker 恢复密钥，根据某些体系结构组件的可用性，这种失败可能会导致停机以及潜在的数据丢失。

> [!WARNING]
> 检索 BitLocker 恢复密钥，并将其存储在 Azure Stack 外部的安全位置。 在某些支持方案中，不提供恢复密钥可能会导致数据丢失，并需要从备份映像还原系统。

如果你怀疑系统中出现 BitLocker 问题（例如 Azure Stack 无法启动），请与支持人员联系。 支持人员需要你的 BitLocker 恢复密钥。 对该特定 VM/主机/卷执行 FRU 操作可以解决大多数 BitLocker 相关问题。 对于其他情况，可以使用 BitLocker 恢复密钥执行手动解锁过程。 如果 BitLocker 恢复密钥不可用，唯一的做法是从备份映像进行还原。 根据上次执行备份的时间，可能会丢失数据。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Stack 安全性](azure-stack-security-foundations.md)
- 有关 BitLocker 如何保护 CSV 的详细信息，请参阅[使用 BitLocker 保护群集共享卷和存储区域网络](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker)。