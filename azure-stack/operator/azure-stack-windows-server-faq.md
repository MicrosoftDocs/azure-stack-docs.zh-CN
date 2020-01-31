---
title: Azure Stack 中心 Windows Server 相关常见问题解答
description: 适用于 Windows Server 的 Azure Stack 集线器 Marketplace 常见问题解答列表
author: sethmanheim
ms.topic: article
ms.date: 12/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 5a0c02737610a663448286b16afbf1504102a8a6
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890130"
---
# <a name="windows-server-in-azure-stack-hub-marketplace-faq"></a>Azure Stack 中心 Marketplace 中的 Windows Server 应用常见问题

本文解答了有关[Azure Stack 中心 Marketplace](azure-stack-marketplace.md)中的 Windows Server 映像的常见问题。

## <a name="marketplace-items"></a>Marketplace 项

### <a name="how-do-i-update-to-a-newer-windows-image"></a>如何实现更新为较新的 Windows 映像吗？

首先，确定是否有任何 Azure 资源管理器模板引用特定版本。 如果是这样，请更新这些模板，或者保留旧的映像版本。 最好使用**版本：最新版本**。

接下来，如果任何虚拟机规模集指的是特定版本，则应考虑是否将在以后对其进行缩放，并决定是否保留旧版本。 如果这两个条件都不适用，请在下载新映像之前删除 marketplace 中的旧映像。 使用 marketplace 管理将其删除（如果原始的下载方式）。 然后下载较新版本。

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Azure Stack 集线器上的 Windows Server Marketplace 映像的授权选项有哪些？

Microsoft 通过 Azure Stack 中心市场提供了两个版本的 Windows Server 映像。 Azure Stack 中心环境中只能使用此映像的一个版本。  

- 即**用即付**：这些图像运行完整的 Windows 计量标准。
   谁应使用：使用*消费计费模型*的企业协议（EA）客户;不希望使用 SPLA 授权的 Csp。
- **自带许可（BYOL）** ：这些映像运行基本计量。
   谁应使用：具有 Windows Server 许可证的 EA 客户;使用 SPLA 授权的 Csp。

Azure Stack 集线器不支持 Azure 混合使用权益（AHUB）。 通过 "容量" 模型进行许可的客户必须使用 BYOL 映像。 如果使用 Azure Stack 开发工具包（ASDK）进行测试，则可以使用这些选项中的任何一个。

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>如果我下载了错误的版本以提供我的租户/用户，该怎么办？

首先通过 marketplace 管理删除不正确的版本。 等待它完成（查看通知完成，而不是**Marketplace 管理**边栏选项卡）。 然后下载正确的版本。

如果下载这两个版本的映像，则 marketplace 库中的最终客户只能看到最新版本。

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>如果我的用户在以前的 Windows 版本中未正确检查 "我有许可证"，并且没有许可证，该怎么办？

您可以通过运行以下脚本，更改许可证模型属性，使其从 "自带许可证（BYOL）" 切换到 "即用即付" （PAYG）模型：

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

可以通过运行以下命令来检查 VM 的许可证类型。 如果许可证模型显示 " **Windows_Server**"，则将根据 BYOL 价格向你收费，否则将按 PAYG 模式为 Windows 计量器收费：

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>如果我有一个较旧的映像，而我的用户忘记选中 "我有许可证" 框，或者我们使用自己的映像，并且我们有企业协议的权利，那该怎么办？

可以通过运行以下命令，将许可证模型属性更改为自带许可证模型：

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>使用 Windows Server 的其他虚拟机（如 SQL 或 Machine Learning Server）呢？

这些映像确实会应用**licenseType**参数，因此它们按你使用的方式付费。 可以设置此参数（请参阅前面的 FAQ 答案）。 这仅适用于 Windows Server 软件，不适用于 SQL 之类的分层产品，要求你自带许可证。 使用许可时请务必使用分层软件产品。

请注意，如果版本为 XX，则只能更改 Marketplace 中 SQL Server 映像的**licenseType**属性。20190410或更高版本。 如果你从 Marketplace 运行 SQL Server 映像的旧版本，则无法更改**licenseType**属性，并且必须使用 Marketplace 中的最新 SQL Server 映像重新部署。

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>我有企业协议（EA），并且将使用我的 EA Windows Server 许可证;如何确保正确地对图像计费？

可以在 Azure 资源管理器模板中添加**licenseType： Windows_Server** 。 必须将此设置添加到每个虚拟机资源块。

## <a name="activation"></a>激活

若要在 Azure Stack 集线器上激活 Windows Server 虚拟机，必须满足以下条件：

- OEM 已在 Azure Stack 集线器中的每个主机系统上设置相应的 BIOS 标记。
- Windows Server 2012 R2 和 Windows Server 2016 必须使用[虚拟机自动激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 Azure Stack 中心不支持密钥管理服务（KMS）和其他激活服务。

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>如何验证虚拟机是否已激活？

在提升的命令提示符下运行以下命令：

```shell
slmgr /dlv
```

如果它已正确激活，你会看到这种情况清楚，并显示在 `slmgr` 输出中。 不要依赖于显示的水印，因为它可能不是最新的，或者显示在你的不同虚拟机后面。

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>我的 VM 未设置为使用 AVMA，如何修复？

在提升的命令提示符下运行以下命令：

```shell
slmgr /ipk <AVMA key>
```

有关用于映像的密钥，请参阅[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))一文。

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>我创建自己的 Windows Server 映像，如何确保它们使用 AVMA？

建议你在运行 `sysprep` 命令之前，先使用适当的密钥执行 `slmgr /ipk` 命令行。 或者，在任何 Unattend.xml 安装文件中包含 AVMA 键。

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>我尝试使用在 Azure 上创建的我的 Windows Server 2016 映像，但未激活或使用 KMS 激活。

运行 `slmgr /ipk` 命令。 Azure 映像可能无法正确回退到 AVMA，但如果他们能够访问 Azure KMS 系统，它们将会激活。 建议确保将这些 Vm 设置为使用 AVMA。

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>我已经执行了所有这些步骤，但虚拟机仍未激活。

请与硬件供应商联系，验证是否安装了正确的 BIOS 标记。

### <a name="what-about-earlier-versions-of-windows-server"></a>Windows Server 的早期版本是什么？

Windows Server 的早期版本不支持[虚拟机自动激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 必须手动激活 Vm。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Azure Stack 中心市场概述](azure-stack-marketplace.md)
- [将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)
