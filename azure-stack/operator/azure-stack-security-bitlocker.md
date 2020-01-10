---
title: 静态数据加密
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 集线器如何通过静态加密来保护数据。
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 36ce8984ba8c4dafecaff9d985b223e320a9fa2c
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814994"
---
# <a name="data-at-rest-encryption-in-azure-stack-hub"></a>Azure Stack 集线器中的静态数据加密

Azure Stack 集线器使用静态加密在存储子系统级别保护用户和基础结构数据。 使用带有128位 AES 加密的 BitLocker 加密 Azure Stack 集线器的存储子系统。 BitLocker 密钥保存在内部密钥存储中。

静态数据加密是对许多主要符合性标准（例如，PCI-DSS、FedRAMP、HIPAA）的常见要求。 Azure Stack 集线器使你可以满足这些要求，无需额外的工作或配置。 有关 Azure Stack 中心如何帮助满足合规性标准的详细信息，请参阅[Microsoft 服务信任门户](https://aka.ms/AzureStackCompliance)。

> [!NOTE]
> 静态数据加密可以保护数据免受物理上窃取一个或多个硬盘驱动器的人员的访问。 静态数据加密不能防止通过网络截获的数据（传输中的数据）、当前使用的数据（内存中的数据），或者更常见的情况是在系统启动并运行时 exfiltrated 数据。

## <a name="retrieving-bitlocker-recovery-keys"></a>正在检索 BitLocker 恢复密钥

静态数据的 Azure Stack 中心 BitLocker 密钥在内部进行管理。 无需为常规操作或系统启动期间提供。 但是，支持方案可能要求 BitLocker 恢复密钥使系统处于联机状态。  

> [!WARNING]
> 检索 BitLocker 恢复密钥并将其存储在 Azure Stack 中心以外的安全位置。 在某些支持方案中没有恢复密钥可能会导致数据丢失，并且需要从备份映像还原系统。

检索 BitLocker 恢复密钥需要访问[特权终结点](azure-stack-privileged-endpoint.md)（PEP）。 在 PEP 会话中，运行 AzsRecoveryKeys cmdlet。

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

*AzsRecoveryKeys* cmdlet 的可选参数：

| 参数 | Description | 类型 | 需要 |
|---------|---------|---------|---------|
|*raw* | 返回每个加密卷的恢复密钥、计算机名称和密码 id 之间的映射的原始数据。  | 开关 | 否（设计用于支持方案）|

## <a name="troubleshoot-issues"></a>排查问题

在极大的情况下，BitLocker 解锁请求可能会失败，导致无法启动特定卷。 如果没有 BitLocker 恢复密钥，此故障可能会导致停机，并可能导致数据丢失，具体取决于体系结构的某些组件的可用性。

> [!WARNING]
> 检索 BitLocker 恢复密钥并将其存储在 Azure Stack 中心以外的安全位置。 在某些支持方案中没有恢复密钥可能会导致数据丢失，并且需要从备份映像还原系统。

如果怀疑系统遇到了 BitLocker 问题（例如 Azure Stack 集线器未能启动），请联系支持人员。 支持需要你的 BitLocker 恢复密钥。 对于该特定 VM/主机/卷，可以使用 FRU 操作来解决大部分 BitLocker 相关问题。 对于其他情况，可以执行手动解锁过程，使用 BitLocker 恢复密钥。 如果 BitLocker 恢复密钥不可用，则唯一的选择是从备份映像还原。 根据上一次备份的完成时间，你可能会遇到数据丢失。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Stack 中心安全性](azure-stack-security-foundations.md)。
- 有关 BitLocker 如何保护 Csv 的详细信息，请参阅[通过 BitLocker 保护群集共享卷和存储区域网络](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker)。