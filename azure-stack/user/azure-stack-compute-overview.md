---
title: Azure Stack Hub VM 简介
description: 了解 Azure Stack Hub VM。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: 70bbb35b8433e8542fc58303f1e8cc56c28c84d7
ms.sourcegitcommit: 824fd33fd5d6aa0c0dac06c21b592bdb60378940
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99850799"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Azure Stack Hub VM 简介

Azure Stack Hub 以按需可缩放的计算资源的形式提供虚拟机 (VM)。 如果需要以更大的力度控制计算环境，可以选择 VM。 本文详细介绍了如何创建第一个 VM。

Azure Stack Hub VM 可提供虚拟化的灵活性，而无需管理群集或单个计算机。 不过，仍然需要通过执行任务（例如，配置、修补/更新和安装在 VM 上运行的软件）来维护 VM。

可通过多种方式使用 Azure Stack Hub VM。 例如：

- **开发和测试**：Azure Stack Hub VM 可让你使用编写和测试应用程序所需的特定配置创建计算机。

- **云中的应用程序**：由于应用程序的需求会不断变化，在 Azure Stack Hub 中的 VM 上运行应用程序可能会较具经济效益。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。

- **扩展的数据中心**：Azure Stack Hub 虚拟网络中的 VM 可以连接到组织的网络或 Azure。

可以根据需要，将应用程序使用的 VM 纵向或横向扩展为任意数目。

## <a name="before-creating-a-vm"></a>创建 VM 之前

在 Azure Stack Hub 中构建应用程序基础结构时，始终要考虑设计注意事项。 在开始创建基础结构之前，必须考虑到 VM 的以下重要方面：

- 应用程序资源的名称。
- VM 的大小。
- 可以创建的 VM 数目上限。
- VM 运行的操作系统。
- VM 在启动后的配置。
- VM 所需的相关资源。

### <a name="names"></a>名称

VM 会被指定名称，也具有在操作系统中所配置的计算机名称。 VM 的名称最多可包含 15 个字符。

如果使用 Azure Stack Hub 创建操作系统磁盘，则计算机名称与 VM 名称相同。 如果上传并使用自己的映像（该映像包含先前配置的操作系统），并使用它创建 VM，则名称可能会不同。 上传自己的映像文件时，最佳做法是确保操作系统中的计算机名称与 VM 名称匹配。

### <a name="vm-size"></a>VM 大小

使用的 VM 大小取决于要运行的工作负荷。 然后，选择的大小决定了处理能力、内存和存储容量等因素。 Azure Stack Hub 提供各种大小来支持多种类型的用途。

### <a name="vm-limits"></a>VM 限制

订阅设有默认的配额限制，可能会影响如何部署项目的 VM。 每个订阅的当前限制是每区域 20 个 VM。

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像

Azure Stack Hub 中的 VM 限制为第 1 代虚拟硬盘 (VHD/VHDX) 格式。 VHD 可用来存储计算机操作系统 (OS) 和数据。 VHD 还可用于存储安装 OS 时使用的映像。 Azure Stack Hub 提供一个市场，适用于各种版本和类型的操作系统。 市场映像由映像发布者、套餐、SKU 和版本（通常将最新版本指定为 **最新**）标识。

下表显示了如何查找映像的信息：

|方法|说明|
|---------|---------|
|Azure Stack Hub 门户|选择要使用的映像时，系统会自动指定值。|
|Azure Stack Hub PowerShell|`Get-AzVMImagePublisher -Location "location"`<br>`Get-AzVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[列出映像发布者](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[列出映像产品](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[列出映像 SKU](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

可以选择上传并使用自己的映像。 如果这样做，则不会使用发布者名称、套餐和 SKU。

### <a name="extensions"></a>扩展

VM 扩展通过部署后配置和自动化任务来增加 VM 功能。 可以使用扩展完成以下常见任务：

- **运行自定义脚本**：预配 VM 时，自定义脚本扩展可以通过运行脚本，帮助在 VM 上配置工作负荷。

- **部署和管理配置**：可以借助 PowerShell Desired State Configuration (DSC) 扩展在 VM 上设置用于管理配置和环境的 DSC。

- **收集诊断数据**：Azure 诊断扩展可帮助你配置 VM 来收集诊断数据，用于监视应用程序的运行状况。

### <a name="related-resources"></a>相关资源

下表中的资源由 VM 使用，在创建 VM 时必须存在或已创建：

|资源|必须|说明|
|---------|---------|---------|
|资源组|是|VM 必须包含在资源组中。|
|存储帐户|否|如果使用托管磁盘，则 VM 不需要存储帐户来存储其虚拟硬盘。 |
|虚拟网络|是|VM 必须是虚拟网络的成员。|
|公共 IP 地址|否|可以向 VM 分配一个公共 IP 地址，以便远程访问它。|
|Linux|是|VM 需要使用网络接口在网络中通信。|
|数据磁盘数|否|VM 可以包含数据磁盘，以便扩展存储功能。|

## <a name="create-your-first-vm"></a>创建第一个 VM

有多种方法可创建 VM。 你的选择取决于环境。 下表提供信息来帮助你开始创建 VM：

|方法|文章|
|---------|---------|
|Azure Stack Hub 门户|[使用 Azure Stack Hub 门户创建 Windows VM](azure-stack-quick-windows-portal.md)。<br>[使用 Azure Stack Hub 门户创建 Linux VM](azure-stack-quick-linux-portal.md)。|
|模板|Azure Stack Hub 快速入门模板位于以下位置：<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://aka.ms/aa6z60s)|
|PowerShell|[在 Azure Stack Hub 中使用 PowerShell 创建 Windows VM](azure-stack-quick-create-vm-windows-powershell.md)<br>[在 Azure Stack Hub 中使用 PowerShell 创建 Linux VM](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[在 Azure Stack Hub 中使用 CLI 创建 Windows VM](azure-stack-quick-create-vm-windows-cli.md)<br>[在 Azure Stack Hub 中使用 CLI 创建 Linux VM](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>管理 VM

可以使用基于浏览器的门户、支持脚本的命令行工具或直接通过 API 管理 VM。 一些典型的管理任务包括：

- 获取有关 VM 的信息。
- 连接到 VM。
- 管理可用性。
- 进行备份。

### <a name="get-information-about-your-vm"></a>获取有关 VM 的信息

下表显示了可获取 VM 相关信息的一些方法：

|方法|说明|
|---------|---------|
|Azure Stack Hub 门户|在中心菜单中，单击“虚拟机”，然后从列表中选择 VM。 在 VM 的页面上，可以访问概述信息、设置值以及监视指标。|
|Azure PowerShell|在 Azure 和 Azure Stack Hub 中，管理 VM 的方法很相似。 若要详细了解如何使用 PowerShell，请参阅 Azure 主题：[使用 Azure PowerShell 模块创建和管理 Windows VM](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)。|
|客户端 SDK|在 Azure 和 Azure Stack Hub 中，使用 C# 管理 VM 的方法很相似。 有关详细信息，请参阅[在 Azure 中使用 C# 创建和管理 Windows VM](/azure/virtual-machines/windows/csharp)。|

### <a name="connect-to-your-vm"></a>连接到 VM

在 Azure Stack Hub 门户中，可以使用“连接”选项连接到 VM。

## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub 中的 VM 注意事项](azure-stack-vm-considerations.md)
