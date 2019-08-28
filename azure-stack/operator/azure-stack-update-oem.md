---
title: 将原始设备制造商 (OEM) 更新应用到 Azure Stack |Microsoft Docs
description: 了解如何将原始设备制造商 (OEM) 更新应用到 Azure Stack。
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
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 73671431c70960fa517ee83c68945f14e2621b46
ms.sourcegitcommit: 9cb82df1eccb0486bcabec0bd674162d4820c00c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060184"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>应用 Azure Stack 原始设备制造商 (OEM) 更新

适用范围：*Azure Stack 集成系统*

你可以将原始设备制造商 (OEM) 更新应用于 Azure Stack 硬件组件, 以接收驱动程序和固件改进以及安全修补程序, 同时最大程度地降低对用户的影响。 在本文中, 可以了解 OEM 更新、OEM 联系信息以及如何应用 OEM 更新。

## <a name="overview-of-oem-updates"></a>OEM 更新概述

除了 Microsoft Azure Stack 更新, 许多 Oem 还会发布 Azure Stack 硬件 (如驱动程序和固件更新) 的定期更新。 这称为**OEM 包更新**。 若要了解 OEM 是否要发布 OEM 包更新, 请查看[oem 的 Azure Stack 文档](#oem-contact-information)。

这些 OEM 包更新将上传到**updateadminaccount**存储帐户, 并通过 Azure Stack 管理员门户应用。 有关详细信息, 请参阅[应用 OEM 更新](#apply-oem-updates)。

询问原始设备制造商 (OEM) 有关其特定通知流程的信息, 以确保 OEM 包更新通知可访问你的组织。

一些硬件供应商可能需要*硬件供应商 VM*来处理内部固件更新过程。 有关详细信息, 请参阅[配置硬件供应商 VM](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>OEM 联系信息 

本部分包含 OEM 联系信息以及到 OEM Azure Stack 参考资料的链接。

| 硬件伙伴 | 地区 | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | 全部 | [Cisco Azure Stack 支持/固件更新-自动通知 (需要帐户/登录)](https://software.cisco.com/download/redirect?i=!y&mdfid=283862063&softwareid=286320368&release=1.0(0)&os=)<br><br>[适用于 Microsoft Azure Stack 的 Cisco 集成系统的发行说明](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | 全部 | [适用于 Microsoft Azure Stack 14G 的云 (需要帐户和登录)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[适用于 Microsoft Azure Stack 13G 的云 (需要帐户和登录)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员 (要求提供帐户和登录名)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | 欧洲、中东和非洲 | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (需要帐户和登录名)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | 全部 | [HPE ProLiant for Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | 全部 | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/固件包](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[terra Azure Stack 文档 (包括 FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>应用 OEM 更新

使用以下步骤应用 OEM 包:

1. 你需要联系你的 OEM 来:
      - 确定 OEM 包的当前版本。  
      - 找到下载 OEM 包的最佳方法。  
2. 准备 OEM 包, 其中包含[下载集成系统更新包](azure-stack-servicing-policy.md)中概述的步骤。
3. 使用[Azure Stack 中的 "应用更新" 中](azure-stack-apply-updates.md)所述的步骤来应用更新。

## <a name="configure-hardware-vendor-vm"></a>配置硬件供应商 VM

一些硬件供应商可能需要 VM 来帮助 OEM 更新过程。 你的硬件供应商将负责创建这些 vm, 并记录在运行`ProxyVM` OEMExternalVM `HardwareManager` cmdlet 时是否需要或进行 **VMType** , 以及应该将哪些凭据用于 **-凭据**。 创建 Vm 后, 请将其配置为具有特权终结点中的**OEMExternalVM** 。

有关 Azure Stack 上的特权终结点的详细信息, 请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

1.  访问特权终结点。

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. 使用**OEMExternalVM** cmdlet 配置硬件供应商 VM。 Cmdlet 用于验证 **-VMType** `ProxyVM`的 IP 地址和凭据。 VMType`HardwareManager` cmdlet 不会验证输入。 提供给**OEMExternalVM**的 **-Credential**参数是由硬件供应商文档明确记录的。  它不是用于特权终结点或任何其他现有 Azure Stack 凭据的 CloudAdmin 凭据。

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
