---
title: 将资源管理器模板用于 Azure Stack 中心的托管磁盘
description: 了解使用 Azure 资源管理器模板时托管和非托管磁盘之间的差异。
author: sethmanheim
ms.author: sethm
ms.date: 8/25/2020
ms.topic: conceptual
ms.reviewer: wellsluo
ms.lastreviewed: 8/25/2020
ms.openlocfilehash: 2d2f274fffd1a07857b79255587b659003a3a1ce
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823245"
---
# <a name="use-vm-managed-disks-templates"></a>使用 VM 托管磁盘模板

本文介绍了使用 Azure 资源管理器模板在 Azure Stack 中心中预配虚拟机时，托管磁盘与非托管磁盘之间的差异。 这些示例可帮助你将使用非托管磁盘的现有模板转换为托管磁盘。

## <a name="unmanaged-disks-template-formatting"></a>非托管磁盘模板格式

在开始之前，我们先了解一下非托管磁盘的部署方式。 创建非托管磁盘时，需要一个用于保存 VHD 文件的存储帐户。 可以创建新存储帐户，或使用现有的存储帐户。 在模板的 resources 块中创建新的存储帐户资源，如下所示：

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2017-10-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

在虚拟机对象中，在存储帐户中添加一个依赖项才能确保先创建存储帐户，再创建虚拟机。 然后，在 `storageProfile` 节中，指定 VHD 位置的完整 URI，该 URI 引用存储帐户，并且 OS 磁盘和任何数据磁盘都需要它。 以下示例从映像创建一个 OS 磁盘，并创建一个1023GB 大小的空数据磁盘：

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>托管磁盘模板的格式

使用 Azure 托管磁盘，该磁盘将成为顶级资源，不再需要用户创建存储帐户。 托管磁盘是在 API 版本中首次引入的 `2017-03-30` 。 以下部分将演练默认设置，并说明如何进一步自定义磁盘。

### <a name="default-managed-disk-settings"></a>默认的托管磁盘设置

若要使用托管磁盘创建 VM，则不再需要创建存储帐户资源。 下面的模板示例与以前的非托管磁盘示例有一些差异：

- `apiVersion`是支持托管磁盘的 "virtualMachines" 资源类型的版本。
- `osDisk` 和 `dataDisks` 不再引用 VHD 的特定 URI。
- 在部署时，如果不指定其他属性，磁盘将根据 VM 的大小使用存储类型。 例如，如果你使用的 VM 大小支持高级存储 (大小为 "s"，如 Standard_DS2_v2) ，则默认情况下会配置高级磁盘。 若要更改此行为，可以使用磁盘的 SKU 设置来指定存储类型。
- 如果没有为磁盘指定名称，则 OS 磁盘采用格式 `<VMName>_OsDisk_1_<randomstring>`，每个数据磁盘采用格式 `<VMName>_disk<#>_<randomstring>`。
  - 如果要从自定义映像创建 VM，则会从自定义映像资源中定义的磁盘属性中检索存储帐户类型和磁盘名称的默认设置。 可以通过在模板中指定这些值来替代它们。
- 默认情况下，磁盘缓存是操作系统磁盘的 **读/写** ， **无** 适用于数据磁盘。
- 在下面的示例中，仍有一个存储帐户依赖项，但这仅适用于诊断的存储，而对于磁盘存储则不需要：

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="use-a-top-level-managed-disk-resource"></a>使用顶级托管磁盘资源

在虚拟机对象中指定磁盘配置的一种替代方法是创建一个顶级磁盘资源，并在创建虚拟机的过程中附加该资源。 请确保将 `2017-03-30` 用作 `disks` 资源 API 版本。 例如，可按如下所示创建一个用作数据磁盘的磁盘资源。 在此示例中， `vmName` 将用作磁盘名称的一部分：

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2017-03-30",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

在 VM 对象中，引用要附加的磁盘对象。 指定在 `managedDisk` 属性中创建的托管磁盘的资源 ID 可以在创建 VM 时附加该磁盘。 该 VM 资源的 `apiVersion` 设置为 `2017-12-01`。 添加了磁盘资源上的依赖项，以确保在创建 VM 之前成功创建它：

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>使用托管磁盘创建包含 VM 的托管可用性集

若要使用托管磁盘创建包含 VM 的托管可用性集，请将 `sku` 对象添加到可用性集资源，并将 `name` 属性设置为 `Aligned`。 该属性可确保每个 VM 的磁盘彼此充分隔离，避免发生单点故障。 另请注意， `apiVersion` 可用性集资源的设置为 `2017-12-01` ：

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 1,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

## <a name="next-steps"></a>后续步骤

<!--
* For full templates that use managed disks visit the following Azure Quickstart Repo links.
    * [Windows VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
-->

- 请参阅 [Azure Stack 集线器托管磁盘](azure-stack-managed-disk-considerations.md) ，详细了解托管磁盘。
- 请参阅 [virtualMachines 模板参考](/azure/templates/microsoft.compute/2017-12-01/virtualmachines)中的虚拟机资源的模板参考文档。
- 查看 [Microsoft. 计算/磁盘模板参考](/azure/templates/microsoft.compute/2017-03-30/disks) 文档中有关磁盘资源的模板参考文档。
