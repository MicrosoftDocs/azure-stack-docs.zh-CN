---
title: 将原始设备制造商（OEM）更新应用到 Azure Stack 中心
description: 了解如何将原始设备制造商（OEM）更新应用到 Azure Stack 中心。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/15/2019
ms.author: inhenkel
ms.lastreviewed: 03/04/2020
ms.reviewer: ppacent
ms.openlocfilehash: 982c62ad37d2d19dd3273c7ece3f20ff725ce6b6
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367244"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>应用 Azure Stack 中心原始设备制造商（OEM）更新

你可以将原始设备制造商（OEM）更新应用于 Azure Stack 集线器硬件组件，以接收驱动程序和固件改进以及安全修补程序，同时最大程度地降低对用户的影响。 在本文中，可以了解 OEM 更新、OEM 联系信息以及如何应用 OEM 更新。

## <a name="overview-of-oem-updates"></a>OEM 更新概述

除了 Microsoft Azure Stack 中心更新外，许多 Oem 还会发布 Azure Stack 集线器硬件（如驱动程序和固件更新）的定期更新。 这称为**OEM 包更新**。 若要了解 OEM 是否要发布 OEM 包更新，请查看[oem Azure Stack 中心文档](#oem-contact-information)。

这些 OEM 包更新将上传到**updateadminaccount**存储帐户，并通过 Azure Stack 中心管理员门户应用。 有关详细信息，请参阅[应用 OEM 更新](#apply-oem-updates)。

询问原始设备制造商（OEM）有关其特定通知流程的信息，以确保 OEM 包更新通知可访问你的组织。

一些硬件供应商可能需要*硬件供应商 VM*来处理内部固件更新过程。 有关详细信息，请参阅[配置硬件供应商 VM](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>OEM 联系信息 

本部分包含 OEM 联系信息以及到 OEM Azure Stack 中心参考资料的链接。

| 硬件伙伴 | 区域 | 代码 |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [适用于 Microsoft Azure Stack 集线器操作指南的 Cisco 集成系统](https://aka.ms/aa708e2)<br><br>[UCS C 系列机架装载 UCS 托管服务器软件](https://aka.ms/aa700rq) |
| Dell EMC | All | [适用于 Microsoft Azure Stack 中心14G 的云（需要帐户和登录）](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[适用于 Microsoft Azure Stack 中心13G 的云（需要帐户和登录）](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员（要求提供帐户和登录名）](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA &AMP; US | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [Microsoft Azure Stack 中心的 HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/固件包](https://aka.ms/AA6z600)<br>[terra Azure Stack 中心文档（包括 FRU）](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>应用 OEM 更新

使用以下步骤应用 OEM 包：

> [!IMPORTANT]
> 在 Azure Stack 中心应用更新之前，请确保已完成[更新前清单](release-notes-checklist.md)中的**所有**步骤，并为要应用的更新类型计划了相应的维护时段。

1. 你需要联系你的 OEM 来：
      - 确定 OEM 包的当前版本。  
      - 找到下载 OEM 包的最佳方法。  
2. 在应用 OEM 包更新之前，应始终应用系统当前 Azure Stack 中心版本可用的最新 Azure Stack 中心修补程序。 有关修补程序的详细信息，请参阅[Azure Stack 中心修补程序](azure-stack-servicing-policy.md)。
3. 准备 OEM 包，其中包含[下载集成系统更新包](azure-stack-servicing-policy.md)中概述的步骤。
4. 使用[Azure Stack 集线器中的应用更新中](azure-stack-apply-updates.md)所述的步骤来应用更新。

## <a name="configure-hardware-vendor-vm"></a>配置硬件供应商 VM

一些硬件供应商可能需要 VM 来帮助 OEM 更新过程。 你的硬件供应商将负责创建这些 Vm，并记录在运行**OEMExternalVM** cmdlet 时是否需要 `ProxyVM` 或 `HardwareManager` **VMType** ，以及应该将哪些凭据用于 **-凭据**。 创建 Vm 后，请将其配置为具有特权终结点中的**OEMExternalVM** 。

有关 Azure Stack 中心上的特权终结点的详细信息，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

1.  访问特权终结点。

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. 使用**OEMExternalVM** cmdlet 配置硬件供应商 VM。 Cmdlet 验证**VMType** `ProxyVM`的 IP 地址和凭据。 对于 **-VMType** `HardwareManager` 该 cmdlet 不会验证输入。 提供给**OEMExternalVM**的 **-Credential**参数是由硬件供应商文档明确记录的。  它不是与特权终结点或任何其他现有 Azure Stack 集线器凭据一起使用的 CloudAdmin 凭据。

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>后续步骤

[Azure Stack 中心更新](azure-stack-updates.md)
