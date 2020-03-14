---
title: Azure Stack 中心 Vm 简介
description: 了解 Azure Stack 中心 Vm。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: 576580732440cabd8ae1c140d13130b81b212d16
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294953"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Azure Stack 中心 Vm 简介

Azure Stack 中心将虚拟机（Vm）作为一种按需和可缩放的计算资源提供。 当需要更好地控制计算环境时，可以选择一个 VM。 本文详细介绍了如何创建第一个 VM。

Azure Stack 中心 VM 提供了虚拟化的灵活性，无需管理群集或单个计算机。 但是，您仍需要通过执行配置、修补/更新和安装其上运行的软件等任务来维护 VM。

可以通过多种方式使用 Azure Stack 集线器 Vm。 例如：

- **开发和测试**：通过 Azure Stack 集线器 vm，你可以创建具有代码和测试应用程序所需的特定配置的计算机。

- **云中的应用程序**：由于应用程序的需求可能会波动，因此在 Azure Stack 集线器中的 VM 上运行它可能会产生经济意义。 需要支付额外的 Vm，并在不需要时将其关闭。

- **扩展数据中心**： Azure Stack 中心虚拟网络中的 vm 可以连接到组织的网络，也可以连接到 Azure。

你的应用程序使用的 Vm 可以向外扩展或向外扩展，以满足你的需求。

## <a name="before-creating-a-vm"></a>创建 VM 之前

在 Azure Stack 集线器中构建应用程序基础结构时，始终存在设计方面的注意事项。 在开始创建基础结构之前，请务必考虑 VM 的以下方面：

- 应用程序资源的名称。
- VM 的大小。
- 可创建的最大 Vm 数。
- VM 运行的操作系统。
- VM 在启动后的配置。
- VM 所需的相关资源。

### <a name="naming"></a>命名

已为 VM 分配一个名称，并且该 VM 的计算机名称配置为操作系统的一部分。 VM 的名称最多可包含 15 个字符。

如果使用 Azure Stack 集线器创建操作系统磁盘，则计算机名称和 VM 名称相同。 如果上传和使用包含以前配置的操作系统的映像，并使用它来创建 VM，则名称可能会不同。 当你上传自己的映像文件时，最佳做法是确保操作系统中的计算机名称与 VM 名称匹配。

### <a name="vm-size"></a>VM 大小

你使用的 VM 的大小由你要运行的工作负荷决定。 然后，所选大小又会影响多个因素，例如处理能力、内存和存储容量。 Azure Stack 中心提供不同种类的大小来支持多种类型的用途。

### <a name="vm-limits"></a>VM 限制

你的订阅具有默认的配额限制，这可能会影响你的项目的 Vm 部署。 每个订阅的当前限制是每区域 20 个 VM。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

Azure Stack 集线器中的 Vm 限制为第一代虚拟硬盘（VHD/VHDX）格式。 Vhd 可用于存储计算机操作系统（OS）和数据。 Vhd 还可用于你从中选择的映像来安装 OS。 Azure Stack 中心提供了一个用于各种版本和操作系统类型的 marketplace。 Marketplace 映像由映像发布者、产品/服务、SKU 和版本标识（通常最新版本指定为**最新**版本）。

下表显示了如何查找图像的信息：

|方法|说明|
|---------|---------|
|Azure Stack 中心门户|选择要使用的映像时，系统会自动指定值。|
|Azure Stack 中心 PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[列出映像发布者](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[列出映像产品](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[列出映像 Sku](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

你可以选择上传并使用自己的映像。 如果这样做，则不会使用发布者名称、产品/服务和 SKU。

### <a name="extensions"></a>扩展

VM 扩展通过部署后的配置和自动化任务为 VM 额外增加了功能。
可以使用扩展完成以下常见任务：

- **运行自定义脚本**：预配 vm 时，自定义脚本扩展可以通过运行脚本，帮助在 vm 上配置工作负荷。

- **部署和管理配置**： PowerShell 所需状态配置（DSC）扩展可帮助你在 VM 上设置 DSC，以管理配置和环境。

- **收集诊断数据**： Azure 诊断扩展可帮助你将 VM 配置为收集诊断数据，这些数据可用于监视应用程序的运行状况。

### <a name="related-resources"></a>相关资源

下表中的资源由 VM 使用，并需要在创建 VM 时存在或创建：

|资源|必选|说明|
|---------|---------|---------|
|资源组|是|VM 必须包含在资源组中。|
|存储帐户|否|如果使用托管磁盘，则 VM 不需要存储帐户来存储其虚拟硬盘。 <br>如果使用非托管磁盘，则 VM 需要存储帐户来存储其虚拟硬盘。|
|虚拟网络|是|VM 必须是虚拟网络的成员。|
|公共 IP 地址|否|可以向 VM 分配一个公共 IP 地址，以便远程访问它。|
|网络接口|是|VM 需要使用网络接口在网络中通信。|
|数据磁盘数|否|VM 可以包含数据磁盘，以便扩展存储功能。|

## <a name="create-your-first-vm"></a>创建第一个 VM

可以通过多种方式来创建 VM。 您的选择取决于您的环境。 下表提供的信息可帮助你开始创建 VM：

|方法|项目|
|---------|---------|
|Azure Stack 中心门户|使用 Azure Stack 中心门户创建 Windows VM<br>[使用 Azure Stack 中心门户创建 Linux VM](azure-stack-quick-linux-portal.md)|
|模板|Azure Stack 集线器快速入门模板位于：<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://aka.ms/aa6z60s)|
|PowerShell|[在 Azure Stack 集线器中使用 PowerShell 创建 Windows VM](azure-stack-quick-create-vm-windows-powershell.md)<br>[在 Azure Stack 集线器中使用 PowerShell 创建 Linux VM](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[在 Azure Stack 集线器中使用 CLI 创建 Windows VM](azure-stack-quick-create-vm-windows-cli.md)<br>[在 Azure Stack 集线器中使用 CLI 创建 Linux VM](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>管理 VM

可以使用基于浏览器的门户、支持脚本的命令行工具或直接通过 Api 管理 Vm。 你可能会执行的一些典型管理任务包括：

- 获取有关 VM 的信息
- 连接到 VM
- 管理可用性
- 进行备份

### <a name="get-information-about-your-vm"></a>获取有关 VM 的信息

下表显示了获取有关 VM 的信息的一些方法。

|方法|说明|
|---------|---------|
|Azure Stack 中心门户|在中心菜单中，单击“虚拟机”，并从列表中选择 VM。 在 VM 的页面上，可以访问概述信息、设置值以及监视指标。|
|Azure PowerShell|管理 Vm 在 Azure 和 Azure Stack 集线器中是类似的。 有关使用 PowerShell 的详细信息，请参阅以下 Azure 主题：<br>[用 Azure PowerShell 模块创建和管理 Windows Vm](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|客户端 SDK|使用C#管理 Vm 在 Azure 和 Azure Stack 集线器中是类似的。 有关详细信息，请参阅以下 Azure 主题：<br>[使用在 Azure 中创建和管理 Windows VmC#](/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>连接到 VM

可以使用 Azure Stack 中心门户中的 "**连接**" 按钮连接到 VM。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中心中的 Vm 的注意事项](azure-stack-vm-considerations.md)
