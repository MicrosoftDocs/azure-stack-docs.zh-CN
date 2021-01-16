---
title: 查找云 ID
description: 如何在 Azure Stack Hub 的“帮助 + 支持”中查找云 ID。
author: PatAltimore
ms.topic: article
ms.date: 10/08/2019
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 6da70761e8a37967d3debdff28b67447596c632a
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255956"
---
# <a name="find-your-cloud-id"></a>查找云 ID

本主题介绍如何使用管理员门户或特权终结点 (PEP) 获取云 ID。 云 ID 是用于跟踪从特定缩放单元上传的支持数据的唯一 ID。 当上传诊断日志进行支持分析时，可以通过云 ID 将这些日志与该缩放单元相关联。

## <a name="use-the-administrator-portal"></a>使用管理员门户

1. 打开管理员门户。 
1. 选择“区域管理”。

   ![仪表板的屏幕截图](./media/azure-stack-automatic-log-collection/dashboard.png)

1. 选择“属性”并复制“缩放单元云 ID”。

   ![具有印花云 ID 的区域属性的屏幕截图](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>使用特权终结点

1. 打开权限提升的 PowerShell 会话，运行以下脚本。 根据环境需要，替换 PEP VM 的 IP 地址和云管理员凭据。 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>后续步骤

* [主动发送日志](./diagnostic-log-collection.md#send-logs-proactively)
* [立即发送日志](./diagnostic-log-collection.md#send-logs-now)