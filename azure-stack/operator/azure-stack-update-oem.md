---
title: 对 Azure Stack 应用原始设备制造商 (OEM) 更新 | Microsoft Docs
description: 了解如何对 Azure Stack 应用原始设备制造商 (OEM) 更新。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: efa10feb63f01166cf93b1835d8e38f99b9191db
ms.sourcegitcommit: df20662e77a6ed0a7eba03f79eb53e8cd4471206
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445162"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>应用 Azure Stack 原始设备制造商 (OEM) 更新

*适用于：Azure Stack 集成系统*

可以将原始设备制造商 (OEM) 更新应用到 Azure Stack 硬件组件，以便接收驱动程序和固件改进以及安全修补程序，同时尽量减少对用户的影响。 本文介绍 OEM 更新、OEM 联系人信息，以及如何应用 OEM 更新。

## <a name="overview-of-oem-updates"></a>OEM 更新概述

除了 Microsoft Azure Stack 更新，许多 Oem 还会发布 Azure Stack 硬件（如驱动程序和固件更新）的定期更新。 这些更新称为“OEM 包更新”。 若要了解 OEM 是否发布了 OEM 包更新，请查看 [OEM 的 Azure Stack 文档](#oem-contact-information)。

这些 OEM 包更新会上传到 **updateadminaccount** 存储帐户中，并通过 Azure Stack 管理员门户应用。 有关详细信息，请参阅[应用 OEM 更新](#apply-oem-updates)。

有关原始设备制造商 (OEM) 为确保 OEM 包更新通知到达你的组织而采取的具体通知流程，请咨询原始设备制造商。

某些硬件供应商可能会要求使用硬件供应商 VM 来处理内部固件更新过程。 有关详细信息，请参阅[配置硬件供应商 VM](#configure-hardware-vendor-vm)。

## <a name="oem-contact-information"></a>OEM 联系人信息 

此部分包含 OEM 联系人信息以及 OEM Azure Stack 参考资料的链接。

| 硬件合作伙伴 | 区域 | 代码 |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [适用于 Microsoft Azure Stack 操作指南的 Cisco 集成系统](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_01000.html)<br><br>[UCS C 系列机架装载 UCS 托管服务器软件](https://software.cisco.com/download/home/283862063/type/286320368/release/2.0(0)) |
| Dell EMC | All | [适用于 Microsoft Azure Stack 14G 的云（需要帐户和登录）](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[适用于 Microsoft Azure Stack 13G 的云（需要帐户和登录）](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员（要求提供帐户和登录名）](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA & US | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [HPE ProLiant for Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/固件包](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[terra Azure Stack 文档（包括 FRU）](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>应用 OEM 更新

按以下步骤应用 OEM 包：

1. 以下事项需联系 OEM：
      - 确定 OEM 包的当前版本。  
      - 查找下载 OEM 包的最佳方法。  
2. 在应用 OEM 包更新之前，应始终应用系统当前 Azure Stack 版本上可用的最新 Azure Stack 修补程序。 有关修补程序的详细信息，请参阅 [Azure Stack 修补程序](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)。
3. 按照[下载集成系统的更新包](azure-stack-servicing-policy.md)中介绍的步骤准备 OEM 包。
4. 使用[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)中介绍的步骤应用更新。

## <a name="configure-hardware-vendor-vm"></a>配置硬件供应商 VM

某些硬件供应商可能会要求使用 VM 来处理 OEM 更新过程。 如果在运行 `ProxyVM`Set-OEMExternalVM`HardwareManager`cmdlet 时需要将 **或** 用于 **-VMType**，以及应将哪个凭据用于 **-Credential**，则硬件供应商将负责创建这些 VM 并进行记录。 这些 VM 创建好以后，请通过特权终结点使用 **Set-OEMExternalVM** 配置它们。

有关 Azure Stack 上的特权终结点的详细信息，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

1.  访问特权终结点。

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. 使用 **Set-OEMExternalVM** cmdlet 配置硬件供应商 VM。 此 cmdlet 针对 **-VMType** `ProxyVM` 验证 IP 地址和凭据。 对于 **-VMType** `HardwareManager`，此 cmdlet 不会验证输入。 提供给 **Set-OEMExternalVM** 的 **-Credential** 参数将由硬件供应商文档明确记录。  它不是用于特权终结点的 CloudAdmin 凭据，也不是任何其他现有 Azure Stack 凭据。

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>后续步骤

[Azure Stack 更新](azure-stack-updates.md)
