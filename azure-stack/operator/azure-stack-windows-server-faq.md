---
title: Azure Stack 中心市场常见问题解答
titleSuffix: Azure Stack Hub
description: 列出有关 Windows Server 的 Azure Stack Hub 市场常见问题解答。
author: sethmanheim
ms.topic: article
ms.date: 03/19/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 95719c6b0651932ab41cef5321db06b77eb4fc63
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80069448"
---
# <a name="azure-stack-hub-marketplace-faq"></a>Azure Stack 中心市场常见问题解答

本文解答了有关[Azure Stack 中心 marketplace](azure-stack-marketplace.md)中的 Marketplace 项的一些常见问题。

## <a name="marketplace-items"></a>市场项

### <a name="who-should-i-contact-for-support-issues-with-azure-stack-hub-marketplace-items"></a>对于 Azure Stack 中心市场项的支持问题，应联系谁？

Azure Marketplace 支持指南还扩展到 Azure Stack 集线器 Marketplace 项。 发布者负责在 Azure Stack 中心市场为其产品提供技术支持。 若要了解有关 Azure Marketplace 项的支持指南的详细信息，请参阅 Azure Marketplace 常见问题解答一文中的[支持部分](/azure/marketplace/marketplace-faq-publisher-guide#customer-support)。

### <a name="how-do-i-update-to-a-newer-windows-image"></a>如何更新到较新的 Windows 映像？

首先，请确定是否有任何 Azure 资源管理器模板引用了特定的版本。 如果有，请更新这些模板，或保留旧的映像版本。 最好使用**版本：最新版本**。

接下来，如果任何虚拟机规模集引用特定版本，则应考虑是否会在以后对其进行缩放，并决定是否保留旧版本。 如果这两个条件都不适用，请在下载更新版本之前，先删除 Azure Stack 集线器 Marketplace 中的旧映像。 如果这是原始下载的方式，请使用 marketplace 管理来删除它们。 然后下载新版本。

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Azure Stack 集线器上的 Windows Server Marketplace 映像的授权选项有哪些？

Microsoft 通过 Azure Stack 中心市场提供了两个版本的 Windows Server 映像。 Azure Stack 中心环境中只能使用此映像的一个版本。  

- 即**付即用（PAYG）**：这些图像运行完整的 Windows 计量标准。
   应使用此选项的人员：使用*消费计费模型*的企业协议（EA）客户;不希望使用 SPLA 授权的 Csp。
- **自带许可（BYOL）**：这些映像运行基本计量。
   应使用此选项的人员：具有 Windows Server 许可证的 EA 客户;使用 SPLA 授权的 Csp。

Azure Stack 集线器不支持 Azure 混合使用权益（AHUB）。 通过“容量”模型获取许可证的客户必须使用 BYOL 映像。 如果要使用 Azure Stack 开发工具包（ASDK）进行测试，则可以使用这些选项中的任何一个。

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>如果下载了错误的版本并将其提供给租户/用户，该怎么办？

请先通过“市场管理”删除错误的版本。 等待删除操作完成（请查看完成通知，而不要查看“市场管理”**** 边栏选项卡）。 然后下载正确的版本。

如果下载这两个版本的映像，则只有最新版本对 Azure Stack 集线器 Marketplace 中的最终客户可见。

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>如果我的用户在旧版 Windows 生成中错误地选中了“我有许可证”框，但他们其实并没有许可证，该怎么办？

可以通过运行以下脚本，更改许可证模型属性，使其从 BYOL 切换到 PAYG 模型：

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

可以通过运行以下命令来检查 VM 的许可证类型。 如果许可证模型显示 " **Windows_Server**"，将按 BYOL 价格收费。 否则，将按 PAYG 模型对 Windows 计量器收费：

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>我有一个旧版映像，而我的用户忘记了选中“我有许可证”框，或者我们使用自己的映像且拥有企业协议权利，该怎么办？

可以通过运行以下命令将许可证模型属性更改为 BYOL 模型：

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>对于使用 Windows Server 的其他 VM （例如 SQL 或 Machine Learning Server），该如何处理？

这些映像确实会应用**licenseType**参数，因此它们是 PAYG。 可以设置此参数（请参阅以前的常见问题解答）。 这只适用于 Windows Server 软件，而不适用于 SQL 等分层产品（需要自带许可证）。 PAYG 许可不适用于分层软件产品。

如果版本为 XX，则只能更改 Azure Stack 中心市场 SQL Server 映像的**licenseType**属性。20190410或更高版本。 如果从 Azure Stack 中心市场运行 SQL Server 映像的旧版本，则无法更改**licenseType**属性，并且必须使用 Azure Stack 中心市场中的最新 SQL Server 映像重新部署。

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>我已签署企业协议 (EA) 且将使用 EA Windows Server 许可证，如何确保映像正确计费？

可以在 Azure 资源管理器模板中添加**licenseType： Windows_Server** 。 必须将此设置添加到每个虚拟机（VM）资源块。

## <a name="activation"></a>激活

若要在 Azure Stack Hub 上激活 Windows Server VM，必须满足以下条件：

- OEM 已在 Azure Stack 集线器中的每个主机系统上设置相应的 BIOS 标记。
- Windows Server 2012 R2 和 Windows Server 2016 必须使用[自动 VM 激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 Azure Stack 集线器不支持密钥管理服务（KMS）和其他激活服务。

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>如何验证是否已激活 VM？

在提升的命令提示符下运行以下命令：

```shell
slmgr /dlv
```

如果它已正确激活，你会看到这种情况清楚，并显示在`slmgr`输出中。 不要依赖于显示的水印，因为它可能不是最新的，或者显示在你的不同 VM 后面。

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>我的 VM 未设置为使用 AVMA，如何修复？

在提升的命令提示符下运行以下命令：

```shell
slmgr /ipk <AVMA key>
```

有关用于映像的密钥，请参阅[自动 VM 激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))一文。

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>我自行创建了 Windows Server 映像，如何确保它们使用 AVMA？

建议你在运行该`slmgr /ipk` `sysprep`命令之前，先使用适当的密钥执行命令行。 或者，将 AVMA 密钥包含在任何 Unattend.exe 安装文件中。

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>我尝试使用在 Azure 上创建的我的 Windows Server 2016 映像，但未激活或无法使用 KMS 激活。

运行 `slmgr /ipk` 命令。 Azure 映像可能无法正确回退到 AVMA，但如果它们可以访问 Azure KMS 系统，则会激活。 建议确保将这些 Vm 设置为使用 AVMA。

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>我已经执行了所有这些步骤，但我的 Vm 仍未激活。

请联系硬件供应商，以确认是否安装了正确的 BIOS 标记。

### <a name="what-about-earlier-versions-of-windows-server"></a>对于早期版本的 Windows Server，如何激活？

Windows Server 的早期版本不支持[自动 VM 激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 必须手动激活这些 VM。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Azure Stack 中心市场概述](azure-stack-marketplace.md)
- [将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)
