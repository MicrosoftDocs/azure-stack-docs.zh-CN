---
title: 更新 Windows Defender 防病毒
titleSuffix: Azure Stack
description: 了解如何在 Azure Stack 上更新 Windows Defender 防病毒
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dc2a58e9b6e8701246a279b1f632ffcdd937c7ca
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780637"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>更新 Azure Stack 上的 Windows Defender 防病毒

[Windows Defender 防病毒](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)是一个提供安全和病毒保护的反恶意软件解决方案。 每个 Azure Stack 基础结构组件（Hyper-v 主机和虚拟机）都受 Windows Defender 防病毒保护。 若要获得最新保护，需要定期更新 Windows Defender 防病毒软件定义、引擎和平台。 如何应用更新取决于你的配置。

## <a name="connected-scenario"></a>已连接的场景

Azure Stack[更新资源提供程序](azure-stack-updates.md#the-update-resource-provider)每天下载反恶意软件定义和引擎更新多次。 每个 Azure Stack 基础结构组件从更新资源提供程序获取更新，并自动应用更新。

对于反恶意软件平台更新，请应用[每月 Azure Stack 更新](azure-stack-apply-updates.md)。 每月 Azure Stack 更新包括每月的 Windows Defender 防病毒平台更新。

## <a name="disconnected-scenario"></a>断开连接方案

 应用[每月 Azure Stack 更新](azure-stack-apply-updates.md)。 每月 Azure Stack 更新包括每月的 Windows Defender 防病毒定义、引擎和平台更新。

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Stack 安全性](azure-stack-security-foundations.md)
