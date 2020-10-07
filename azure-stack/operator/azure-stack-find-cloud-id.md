---
title: 查找云 ID
description: 如何在 Azure Stack Hub 的“帮助 + 支持”中查找云 ID。
author: myoungerman
ms.topic: article
ms.date: 10/08/2019
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e14a2a000b52f09f664112467ce8694134243681
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778221"
---
# <a name="find-your-cloud-id"></a>查找云 ID

本主题介绍如何使用管理员门户或特权终结点 (PEP) 获取云 ID。 Cloud ID 是用于跟踪从特定缩放单位上传的支持数据的唯一 ID。 为支持分析上载诊断日志时，云 ID 就是日志与该缩放单位的关联方式。

## <a name="use-the-administrator-portal"></a>使用管理员门户

1. 打开管理员门户。 
1. 选择 **区域管理**。

   ![仪表板的屏幕截图](./media/azure-stack-automatic-log-collection/dashboard.png)

1. 选择 " **属性** " 并复制 " **Stamp 云 ID**"。

   ![具有印花云 ID 的区域属性的屏幕截图](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>使用特权终结点

1. 打开权限提升的 PowerShell 会话，运行以下脚本。 根据环境需要，将 PEP VM 和云管理员凭据的 IP 地址替换为。 

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

* [主动发送日志](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively)
* [立即发送日志](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now)
