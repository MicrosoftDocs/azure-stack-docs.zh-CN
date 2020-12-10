---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: e456f17c75b129a3f45c64b9b55e75d21ecb2973
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935131"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>安装 Az module 时，虚假引发管理员权限要求错误

- 适用：此问题适用于2002及更高版本
- 原因：从提升的提示符安装模块时，将引发错误。 错误表明， `Administrator rights required` 。
- 修正：关闭会话并启动新的提升的 PowerShell 会话。 请确保没有现有 Az。 在会话中加载的帐户模块。
- 发生次数：通用