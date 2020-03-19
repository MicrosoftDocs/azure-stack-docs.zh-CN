---
title: 查找你的云 ID
description: 如何在 Azure Stack 中心帮助和支持中找到你的云 ID。
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520404"
---
# <a name="find-your-cloud-id"></a>查找你的云 ID

本主题介绍如何使用管理员门户或特权终结点（PEP）获取云 ID。 

## <a name="use-the-administrator-portal"></a>使用管理员门户

1. 打开管理员门户。 
1. 单击 "**区域管理**"。

   ![仪表板的屏幕截图](./media/azure-stack-automatic-log-collection/dashboard.png)

1. 单击 "**属性**" 并复制 " **Stamp 云 ID**"。

   ![带有 Stamp Cloud ID 的区域属性的屏幕截图](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>使用特权终结点

1. 打开提升了权限的 PowerShell 会话并运行以下脚本。 根据环境需要，将 PEP VM 和云管理员凭据的 IP 地址替换为。 

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

* [主动发送日志](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)
* [立即发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)






