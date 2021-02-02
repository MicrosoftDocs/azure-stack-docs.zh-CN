---
title: 为 Azure Stack Hub 开发模板
description: 了解如何开发 Azure 资源管理器模板来实现 Azure 与 Azure Stack Hub 之间的应用可移植性。
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a99f3803f5b8ea8d23913efafd1f8d72005b49bd
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247755"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>使用 Azure 资源管理器开发 Azure Stack Hub 的模板

开发应用时，请务必确保模板可在 Azure 和 Azure Stack Hub 之间移植。 本文提供了有关开发 [Azure 资源管理器模板](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)的注意事项。 使用这些模板，可以在 Azure 中创建应用原型并测试部署，而无需访问 Azure Stack Hub 环境。

## <a name="resource-provider-availability"></a>资源提供程序可用性

你计划部署的模板必须仅使用已 Microsoft Azure 的服务，或 Azure Stack 中心内的预览。

## <a name="public-namespaces"></a>公共命名空间

由于 Azure Stack Hub 托管在数据中心中，它的服务终结点命名空间与 Azure 公有云不同。 因此，如果尝试将 Azure 资源管理器模板部署到 Azure Stack Hub，这些模板中的硬编码公共终结点会失败。 可以使用 `reference` 和 `concatenate` 函数动态构建服务终结点，以便在部署期间从资源提供程序检索值。 例如，不需要在模板中硬编码 `blob.core.windows.net`，检索 [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) 即可动态设置 osDisk.URI  终结点：

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API 版本控制

Azure 服务版本在 Azure 和 Azure Stack Hub 之间可能有所不同。 每个资源都需要有 **apiVersion** 属性，用于定义所提供的功能。 为了确保 API 版本在 Azure Stack Hub 中兼容，以下 API 版本对于每个资源提供程序有效：

| 资源提供程序 | apiVersion |
| --- | --- |
| 计算 |**2015-06-15** |
| 网络 |**2015-06-15**、**2015-05-01-preview** |
| 存储 |**2016-01-01**、**2015-06-15**、**2015-05-01-preview** |
| KeyVault | **2015-06-01** |
| 应用服务 |**2015-08-01** |

## <a name="template-functions"></a>模板函数

Azure 资源管理器[函数](/azure/azure-resource-manager/resource-group-template-functions)提供生成动态模板所需的功能。 例如，可以对如下任务使用函数：

* 连接或修整字符串。
* 引用其他资源的值。
* 对资源进行迭代以部署多个实例。

以下函数在 Azure Stack Hub 中不可用：

* 跳过
* Take

## <a name="resource-location"></a>资源位置

在部署过程中，Azure 资源管理器模板使用 `location` 属性来放置资源。 在 Azure 中，位置是指美国西部或南美地区。 在 Azure Stack Hub 中，位置有所不同，因为 Azure Stack Hub 在数据中心内。 若要确保模板可在 Azure 和 Azure Stack Hub 之间转移，在部署单个资源时应引用资源组位置。 可以使用 `[resourceGroup().Location]` 执行此操作，以确保所有资源均继承资源组位置。 以下代码是在部署存储帐户时使用此函数的示例：

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
