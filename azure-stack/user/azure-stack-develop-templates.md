---
title: 为 Azure Stack 集线器开发模板
description: 了解如何开发 Azure 资源管理器模板来实现 Azure 与 Azure Stack 中心之间的应用可移植性。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 97cf7e77aa2a352d5b297b13fca0959722401530
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884891"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>利用 Azure 资源管理器为 Azure Stack 中心开发模板

开发应用时，必须在 Azure 与 Azure Stack 中心之间具有模板便携性，这一点非常重要。 本文提供了有关开发[Azure 资源管理器模板](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)的注意事项。 使用这些模板，可以在 Azure 中构建应用程序和测试部署，而无需访问 Azure Stack 中心环境。

## <a name="resource-provider-availability"></a>资源提供程序可用性

你计划部署的模板必须仅使用已 Microsoft Azure 的服务，或 Azure Stack 中心内的预览。

## <a name="public-namespaces"></a>公共命名空间

由于 Azure Stack 中心托管在你的数据中心内，因此它的服务终结点命名空间不同于 Azure 公有云。 因此，当你尝试将它们部署到 Azure Stack 中心时，Azure 资源管理器模板中的硬编码公共终结点会失败。 你可以使用 `reference` 和 `concatenate` 函数动态生成服务终结点，以便在部署过程中从资源提供程序检索值。 例如，如果不在模板中对 `blob.core.windows.net` 进行硬编码，请检索[primaryendpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175)以动态设置*osDisk*终结点：

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API 版本控制

Azure 服务版本在 Azure 与 Azure Stack 中心之间可能会有所不同。 每个资源都需要**apiVersion**属性，该属性定义所提供的功能。 为了确保 Azure Stack 集线器中的 API 版本兼容性，以下 API 版本对每个资源提供程序都有效：

| 资源提供程序 | apiVersion |
| --- | --- |
| 计算 |**2015-06-15** |
| 网络 |**2015-06-15**， **2015-05-01-预览版** |
| 存储空间 |**2016-01-01**、 **2015-06-15**、 **2015-05-01-预览** |
| KeyVault | **2015-06-01** |
| 应用服务 |**2015-08-01** |

## <a name="template-functions"></a>模板功能

Azure 资源管理器[函数](/azure/azure-resource-manager/resource-group-template-functions)提供生成动态模板所需的功能。 例如，可以将函数用于如下任务：

* 串联或修整字符串。
* 引用其他资源的值。
* 循环访问资源以部署多个实例。

这些函数在 Azure Stack 集线器中不可用：

* 跳过
* Take

## <a name="resource-location"></a>资源位置

在部署期间，Azure 资源管理器模板使用 `location` 属性来放置资源。 在 Azure 中，位置是指美国西部或南美地区。 Azure Stack 中心中的位置不同，因为 Azure Stack 中心位于你的数据中心。 若要确保模板在 Azure 与 Azure Stack 中心之间可转换，你应在部署单个资源时引用资源组位置。 你可以使用 `[resourceGroup().Location]` 执行此操作，以确保所有资源继承资源组位置。 以下代码是在部署存储帐户时使用此函数的示例：

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
