---
title: 静态数据加密
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack Hub 如何使用静态加密来保护数据。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 08/06/2020
ms.openlocfilehash: 2aa24a852e5388a36b1103c2a61f5aa70930d84d
ms.sourcegitcommit: 1ab1293b594fe8ffc00dc800c663cf1323dc41ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87939524"
---
# <a name="data-at-rest-encryption-in-azure-stack-hub"></a>Azure Stack Hub 中的静态数据加密

Azure Stack Hub 使用静态加密来保护存储子系统级别的用户数据和基础结构数据。 Azure Stack Hub 的存储子系统按照 128 位 AES 加密法使用 BitLocker 进行加密。 BitLocker 密钥保存在内部机密存储中。

静态数据加密是许多主流合规标准（例如 PCI-DSS、FedRAMP、HIPAA）的共同要求。 Azure Stack Hub 使你无需进行额外的工作或配置即可满足这些要求。 有关 Azure Stack Hub 如何帮助你符合合规性标准的详细信息，请参阅 [Microsoft 服务信任门户](https://aka.ms/AzureStackCompliance)。

> [!NOTE]
> 静态数据加密可防止窃取一个或多个硬盘实物的人员访问你的数据。 静态数据加密不能防止通过网络截取数据（传输中的数据），也无法保护当前正在使用的数据（内存中的数据），或者更一般而言，在系统已启动且正在运行时无法防止数据泄露。

## <a name="retrieving-bitlocker-recovery-keys"></a>检索 BitLocker 恢复密钥

静态数据的 Azure Stack Hub BitLocker 密钥在内部进行管理。 你不需要为常规操作或在系统启动期间提供这些密钥。 但是，支持方案可能要求提供 BitLocker 恢复密钥来让系统联机。  

> [!WARNING]
> 检索 BitLocker 恢复密钥，并将其存储在 Azure Stack Hub 外部的安全位置。 在某些支持方案中，不提供恢复密钥可能会导致数据丢失，并需要从备份映像还原系统。

检索 BitLocker 恢复密钥需要访问[特权终结点](azure-stack-privileged-endpoint.md) (PEP)。 在 PEP 会话中运行 Get-AzsRecoveryKeys cmdlet。

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys -raw
```

Get-AzsRecoveryKeys cmdlet 的参数：

| 参数 | 说明 | 类型 | 必选 |
|---------|---------|---------|---------|
|*raw* | 返回每个加密卷的恢复密钥、计算机名和密码 ID 之间映射的数据。  | 开关 | 否，但建议使用 |

## <a name="troubleshoot-issues"></a>排查问题

在极端情况下，BitLocker 解锁请求可能失败，并导致特定卷无法启动。 如果没有 BitLocker 恢复密钥，根据某些体系结构组件的可用性，这种失败可能会导致停机以及潜在的数据丢失。

> [!WARNING]
> 检索 BitLocker 恢复密钥，并将其存储在 Azure Stack Hub 外部的安全位置。 在某些支持方案中，不提供恢复密钥可能会导致数据丢失，并需要从备份映像还原系统。

如果你怀疑系统遇到 BitLocker 问题（例如 Azure Stack Hub 无法启动），请与客户支持联系。 支持人员需要你的 BitLocker 恢复密钥。 对该特定 VM/主机/卷执行 FRU 操作可以解决大多数 BitLocker 相关问题。 对于其他情况，可以使用 BitLocker 恢复密钥执行手动解锁过程。 如果 BitLocker 恢复密钥不可用，唯一的选择是从备份映像进行还原。 根据上一次备份的完成时间，你可能会遇到数据丢失的情况。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Stack Hub 安全性](azure-stack-security-foundations.md)。
- 有关 BitLocker 如何保护 CSV 的详细信息，请参阅[使用 BitLocker 保护群集共享卷和存储区域网络](/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker)。
