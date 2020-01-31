---
title: 使用 Azure Stack 集线器中的模板验证工具
description: 使用模板验证工具检查部署到 Azure Stack 集线器的模板。
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 1690e191e2aa610e69b1e204b9e685e5d8e90187
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884297"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>使用 Azure Stack 集线器中的模板验证工具

使用模板验证工具来检查 Azure 资源管理器[模板](azure-stack-arm-templates.md)是否准备好部署到 Azure Stack 中心。 模板验证工具作为 Azure Stack 中心工具 GitHub 存储库的一部分提供。 使用[从 GitHub 下载工具](../operator/azure-stack-powershell-download.md)中所述的步骤下载 Azure Stack 中心工具。

## <a name="overview"></a>概述

若要验证模板，你必须首先构建一个云功能文件，然后运行验证工具。 从 Azure Stack 中心工具使用以下 PowerShell 模块：

- 在**azurerm.cloudcapabilities**文件夹中： **AzureRM**创建一个云功能 JSON 文件，用于表示 Azure Stack 中心云中的服务和版本。
- 在**azurerm.templatevalidator.psm1**文件夹中： **AzureRM**使用云功能 JSON 文件在 Azure Stack Hub 中测试部署的模板。

## <a name="build-the-cloud-capabilities-file"></a>构建云功能文件

使用模板验证程序之前，请运行**AzureRM. Azurerm.cloudcapabilities** PowerShell 模块以生成 JSON 文件。

>[!NOTE]
> 如果更新集成系统，或添加任何新的服务或虚拟扩展，则应再次运行此模块。

1. 请确保已连接到 Azure Stack 集线器。 可以从 Azure Stack 开发工具包（ASDK）主机完成这些步骤，也可以使用[VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)从工作站连接。
2. 导入**AzureRM. Azurerm.cloudcapabilities** PowerShell 模块：

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. 使用**azurerm.cloudcapabilities** cmdlet 可检索服务版本并创建云功能 JSON 文件。 如果未指定 `-OutputPath`，则将在当前目录中创建**AzureCloudCapabilities 文件。** 使用实际的 Azure 位置：

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>验证模板

使用以下步骤通过**AzureRM. Azurerm.templatevalidator.psm1** PowerShell 模块验证模板。 你可以使用自己的模板，也可以使用[Azure Stack 集线器快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)。

1. 导入**AzureRM. azurerm.templatevalidator.psm1. Hbase-runner.psm1** PowerShell 模块：

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. 运行模板验证程序：

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

模板验证警告或错误显示在 PowerShell 控制台中，并写入到源目录中的 HTML 文件中。 下面的屏幕截图是验证报表的一个示例：

![模板验证报表](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>参数

模板验证程序 cmdlet 支持以下参数。

| 参数 | Description | 需要 |
| ----- | -----| ----- |
| `TemplatePath` | 指定以递归方式查找 Azure 资源管理器模板的路径。 | 是 |
| `TemplatePattern` | 指定要匹配的模板文件的名称。 | 否 |
| `CapabilitiesPath` | 指定云功能 JSON 文件的路径。 | 是 |
| `IncludeComputeCapabilities` | 包括对 IaaS 资源的评估，如 VM 大小和 VM 扩展。 | 否 |
| `IncludeStorageCapabilities` | 包含存储资源（如 SKU 类型）的评估。 | 否 |
| `Report` | 指定生成的 HTML 报表的名称。 | 否 |
| `Verbose` | 将错误和警告记录到控制台。 | 否|

### <a name="examples"></a>示例

此示例验证已下载到本地存储的所有[Azure Stack 中心快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)。 该示例还根据 ASDK 功能验证虚拟机（VM）的大小和扩展：

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>后续步骤

- [将模板部署到 Azure Stack 中心](azure-stack-arm-templates.md)
- [为 Azure Stack 集线器开发模板](azure-stack-develop-templates.md)
