---
title: 启动和停止 ASDK |Microsoft Docs
description: 了解如何启动和停止 Azure Stack 开发工具包（ASDK）。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: e32fc6c58232e21eb6e7c087c2d95623209bc28d
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689842"
---
# <a name="start-and-stop-the-asdk"></a>启动和停止 ASDK
不建议只是重新启动 ASDK 主计算机。 相反，请按照本文中的过程操作，正确关闭并重启 ASDK 服务。

## <a name="stop-azure-stack"></a>停止 Azure Stack 
若要正确关闭 Azure Stack 服务和 ASDK 主机计算机，请使用以下 PowerShell 命令：

1. 在 ASDK 主机计算机上以 AzureStack\AzureStackAdmin 的身份登录。
2. 以管理员身份（而不是 PowerShell ISE）打开 PowerShell。
3. 运行以下命令以建立特权终结点（PEP）会话： 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 接下来，在 PEP 会话中，使用**test-azurestack** cmdlet 停止 Azure Stack 服务并关闭 ASDK 主机计算机：

   ```powershell
   Stop-AzureStack
   ```
5. 查看 PowerShell 输出，以确保在 ASDK 主机关闭之前，所有 Azure Stack 服务都已成功关闭。 关闭过程需花费几分钟时间。

## <a name="start-azure-stack"></a>开始 Azure Stack 
当主机启动时，ASDK services 应自动启动。 但是，ASDK 基础结构服务的启动时间根据 ASDK 主机的硬件配置性能而有所不同。 在某些情况下，所有服务可能需要几个小时才能成功重启。

无论 ASDK 如何关闭，你都应使用以下步骤来验证所有 Azure Stack 服务是否已启动并且在主计算机开机后完全正常运行： 

1. 打开 ASDK 主计算机。 
2. 在 ASDK 主机计算机上以 AzureStack\AzureStackAdmin 的身份登录。
3. 以管理员身份（而不是 PowerShell ISE）打开 PowerShell。
4. 运行以下命令以建立特权终结点（PEP）会话：

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. 接下来，在 PEP 会话中，运行以下命令以检查 Azure Stack 服务的启动状态：

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. 查看输出以确保 Azure Stack 服务已成功重新启动。

若要了解有关正确关闭和重新启动 Azure Stack 服务的建议过程的详细信息，请参阅[启动和停止 Azure Stack](../operator/azure-stack-start-and-stop.md)。

## <a name="troubleshoot-startup-and-shutdown"></a>启动和关闭疑难解答 
Azure Stack 如果在 ASDK 主计算机上通电后，请按照以下步骤操作，请执行以下步骤：

1. 在 ASDK 主机计算机上以 AzureStack\AzureStackAdmin 的身份登录。
2. 以管理员身份（而不是 PowerShell ISE）打开 PowerShell。
3. 运行以下命令以建立特权终结点（PEP）会话：

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 接下来，在 PEP 会话中，运行以下命令以检查 Azure Stack 服务的启动状态：

   ```powershell
   Test-AzureStack
   ```
5. 查看输出并解决任何错误。 有关详细信息，请参阅[运行 Azure Stack 的验证测试](../operator/azure-stack-diagnostic-test.md)。
6. 通过运行**test-azurestack** CMDLET 在 PEP 会话内重新启动 Azure Stack 服务：

   ```powershell
   Start-AzureStack
   ```

如果运行**test-azurestack**导致失败，请访问[Azure Stack 支持论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack)获取 ASDK 故障排除支持。 

## <a name="next-steps"></a>后续步骤 
详细了解 Azure Stack 诊断工具和问题日志记录，请参阅[Azure Stack 诊断工具](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)。
