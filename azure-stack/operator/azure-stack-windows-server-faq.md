---
title: Azure Stack Windows Server 相关的常见问题解答 | Microsoft Docs
description: 列出有关 Windows Server 的 Azure Stack 市场常见问题解答
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: 21364595b30c62f47c293e38bdcb9c5663c56e90
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008321"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Azure Stack 市场中的 Windows Server 常见问题解答

本文解答有关 [Azure Stack 市场](azure-stack-marketplace.md)中 Windows Server 映像的一些常见问题。

## <a name="marketplace-items"></a>市场项

### <a name="how-do-i-update-to-a-newer-windows-image"></a>如何更新到较新的 Windows 映像？

首先，请确定是否有任何 Azure 资源管理器模板引用了特定的版本。 如果有，请更新这些模板，或保留旧的映像版本。 最好是使用 **version: latest**。

接下来，如果有任何虚拟机规模集引用特定的版本，则应考虑将来是否要缩放它们，并确定是否要保留旧版本。 如果上述两个条件都不适用，请先在市场中删除旧映像，然后下载新映像。 如果原始映像是使用“市场管理”下载的，请使用“市场管理”将其删除。 然后下载新版本。

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Azure Stack 上的 Windows Server 市场映像有哪些许可选项？

Microsoft 通过 Azure Stack marketplace 提供了两个版本的 Windows Server 映像。 在 Azure Stack 环境中只能使用此映像的一个版本。  

- **即用即付**：这些映像运行全价 Windows 计量器。
   适合对象：使用消耗量计费模型的企业协议 (EA) 客户、不想要使用 SPLA 许可的 CSP。
- **自带许可证 (BYOL)** ：这些映像运行基本计量器。
   适合对象：具有 Windows Server 许可证的 EA 客户、使用 SPLA 许可的 CSP。

Azure Stack 不支持 Azure 混合使用权益 (AHUB)。 通过“容量”模型获取许可证的客户必须使用 BYOL 映像。 如果你正在使用 Azure Stack 开发工具包 (ASDK) 进行测试，则可以使用上述任一选项。

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>如果下载了错误的版本并将其提供给租户/用户，该怎么办？

请先通过“市场管理”删除错误的版本。 等待删除操作完成（请查看完成通知，而不要查看“市场管理”边栏选项卡）。 然后下载正确的版本。

如果下载了两个版本的映像，则最终客户在市场库中只能看到最新版本。

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>如果我的用户在旧版 Windows 生成中错误地选中了“我有许可证”框，但他们其实并没有许可证，该怎么办？

您可以通过运行以下脚本, 更改许可证模型属性, 使其从 "自带许可证 (BYOL)" 切换到 "即用即付" (PAYG) 模型:

```powershell
vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

可以通过运行以下命令来检查 VM 的许可证类型。 如果许可证模型显示 " **Windows_Server**", 则将按 PAYG 模型为 Windows 许可证付费:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>我有一个旧版映像，而我的用户忘记了选中“我有许可证”框，或者我们使用自己的映像且拥有企业协议权利，该怎么办？

可以通过运行以下命令, 将许可证模型属性更改为自带许可证模型:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>对于使用 Windows Server 的其他 VM （例如 SQL 或 Machine Learning Server），该如何处理？

这些映像应用 **licenseType** 参数，因此它们采用即用即付模式。 可以设置此参数（请参阅以前的常见问题解答）。 这只适用于 Windows Server 软件，而不适用于 SQL 等分层产品（需要自带许可证）。 即付即付许可不适用于分层软件产品。

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>我已签署企业协议 (EA) 且将使用 EA Windows Server 许可证，如何确保映像正确计费？

可将 **licenseType:Windows_Server** 添加到 Azure 资源管理器模板中。 必须将此设置添加到每个虚拟机资源块。

## <a name="activation"></a>激活

若要在 Azure Stack 上激活 Windows Server 虚拟机，必须满足以下条件：

- OEM 已在 Azure Stack 上的每个主机系统上设置相应的 BIOS 标记。
- Windows Server 2012 R2 和 Windows Server 2016 必须使用[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 Azure Stack 不支持密钥管理服务 (KMS) 和其他激活服务。

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>如何验证虚拟机是否已激活？

在提升的命令提示符下运行以下命令：

```shell
slmgr /dlv
```

如果虚拟机已正确激活，则 `slmgr` 输出中会明确指示此状态，并显示主机名。 请不要依赖于显示画面中的水印，因为它们可能不是最新的，或者显示你的虚拟机后面的其他虚拟机的状态。

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>我的 VM 未设置为使用 AVMA，如何解决此问题？

在提升的命令提示符下运行以下命令：

```shell
slmgr /ipk <AVMA key>
```

请参阅[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))一文，获取映像使用的密钥。

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>我自行创建了 Windows Server 映像，如何确保它们使用 AVMA？

建议在运行 `sysprep` 命令之前，结合相应的密钥执行 `slmgr /ipk` 命令行。 或者，将 AVMA 密钥包含在任何 Unattend.exe 安装文件中。

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>我正在尝试使用自己在 Azure 上创建的 Windows Server 2016 映像，但它无法激活或者正在使用 KMS 激活。

运行 `slmgr /ipk` 命令。 Azure 映像可能无法正确回退到 AVMA，但如果它们可以访问 Azure KMS 系统，则会激活。 请确保将这些 VM 设置为使用 AVMA。

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>我已执行上述所有步骤，但虚拟机仍无法激活。

请联系硬件供应商，以确认是否安装了正确的 BIOS 标记。

### <a name="what-about-earlier-versions-of-windows-server"></a>对于早期版本的 Windows Server，如何激活？

早期版本的 Windows Server 不支持[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 必须手动激活这些 VM。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Azure Stack 市场概述](azure-stack-marketplace.md)
- [将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
