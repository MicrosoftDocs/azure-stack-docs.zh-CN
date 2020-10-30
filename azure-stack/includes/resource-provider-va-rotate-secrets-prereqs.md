---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 294f0f5b1f6ac45d53f5d3eebc222b3edba23776
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049660"
---
> [!NOTE]
> 值的机密旋转当前仅通过 PowerShell 支持。 

与 Azure Stack 中心基础结构一样，增值资源提供程序使用内部和外部机密。 机密可以采用多种形式，包括使用 X509 证书维护的密码和加密密钥。 作为操作员，你负责：

- 提供更新的外部机密，如用于保护资源提供程序终结点的新 TLS 证书。
- 定期管理资源提供程序的机密旋转。

准备旋转过程：

1. 在获取/续订 X509 证书之前，请查看 [Azure Stack 中心公钥基础结构 (PKI) ](../operator/azure-stack-pki-certs.md#certificate-requirements) 有关重要必备信息的证书要求，其中包括有关所需 PFX 格式的详细信息。 另外，请查看 " [可选 PaaS 证书" 部分](../operator/azure-stack-pki-certs.md#optional-paas-certificates)中指定的要求，以获取特定值添加资源提供程序。

2. 如果尚未安装，请在继续操作之前 [为 Azure Stack 中心安装 PowerShell Az 模块](../operator/powershell-install-az-module.md) 。 Azure Stack 中心机密旋转需要版本 2.0.2-preview 或更高版本。 有关详细信息，请参阅 [在 Azure Stack 中心中从 AzureRM 迁移到 Azure PowerShell Az](../operator/migrate-azurerm-az.md)。
