---
title: 启动和停止 Azure Stack 开发工具包 (ASDK) | Microsoft Docs
description: 了解如何启动和关闭 Azure Stack 开发工具包 (ASDK)。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 8335782ee8f310fa5d975c746e94651a92a54642
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617541"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>启动和停止 Azure Stack 开发工具包 (ASDK)
建议不要仅仅重启 ASDK 主机。 而是应该遵循本文中的过程正确关闭再重启 ASDK 服务。 

## <a name="stop-azure-stack"></a>停止 Azure Stack 
若要正常关闭 Azure Stack 服务和 ASDK 主机，请使用以下 PowerShell 命令：

1. 以 AzureStack\AzureStackAdmin 身份登录到 ASDK 主机。
2. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
3. 运行以下命令建立特权终结点 (PEP) 会话： 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 接下来，在 PEP 会话中，使用 **Stop-AzureStack** cmdlet 停止 Azure Stack 服务并关闭 ASDK 主机：

   ```powershell
   Stop-AzureStack
   ```
5. 查看 PowerShell 输出，确保在关闭 ASDK 主机之前已正常关闭所有 Azure Stack 服务。 关机过程需要花费几分钟时间。

## <a name="start-azure-stack"></a>启动 Azure Stack 
启动主机时，ASDK 服务应会自动启动。 但是，ASDK 基础结构服务的启动时间根据 ASDK 主机硬件配置的性能而异。 在某些情况下，所有服务可能需要花费好几个小时才能成功重启。

不管 ASDK 是否已关闭，都应该使用以下步骤来验证打开主机后，所有 Azure Stack 服务是否都已启动并完全正常运行： 

1. 打开 ASDK 主机。 
2. 以 AzureStack\AzureStackAdmin 身份登录到 ASDK 主机。
3. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
4. 运行以下命令建立特权终结点 (PEP) 会话：

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. 接下来，在 PEP 会话中，运行以下命令检查 Azure Stack 服务的启动状态：

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. 查看输出，确保 Azure Stack 服务已成功重启。

若要详细了解正常关闭和重启 Azure Stack 服务的建议过程，请参阅[启动和停止 Azure Stack](../operator/azure-stack-start-and-stop.md)。 

## <a name="troubleshoot-startup-and-shutdown"></a>排查启动和关机问题 
如果在打开 ASDK 主机后的两个小时内 Azure Stack 服务未成功启动，请执行以下步骤：

1. 以 AzureStack\AzureStackAdmin 身份登录到 ASDK 主机。
2. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
3. 运行以下命令建立特权终结点 (PEP) 会话：

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 接下来，在 PEP 会话中，运行以下命令检查 Azure Stack 服务的启动状态：

   ```powershell
   Test-AzureStack
   ```
5. 查看输出并解决所有错误。 有关详细信息，请参阅[运行 Azure Stack 的验证测试](../operator/azure-stack-diagnostic-test.md)。
6. 在 PEP 会话中运行 **Start-AzureStack** cmdlet，以重启 Azure Stack 服务：

   ```powershell
   Start-AzureStack
   ```

如果运行 **Start-AzureStack** 导致失败，请访问 [Azure Stack 支持论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack)获取 ASDK 故障排除支持。 

## <a name="next-steps"></a>后续步骤 
若要详细了解 Azure Stack 诊断工具和问题日志记录，请参阅 [Azure Stack 诊断工具](../operator/azure-stack-diagnostics.md)。
