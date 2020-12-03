---
title: 在 Azure Stack Hub 中使用 PowerShell 部署模板
description: 在 Azure Stack Hub 中使用 PowerShell 部署模板。
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: d2ba72b65f8c8b1efe11356b217bd6beb218a375
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525330"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>在 Azure Stack 集线器中使用 PowerShell 部署模板

可以使用 PowerShell 将 Azure 资源管理器模板部署到 Azure Stack Hub。 本文介绍如何使用 PowerShell 部署模板。

## <a name="run-powershell-cmdlets"></a>运行 PowerShell cmdlet

### <a name="az-modules"></a>[Az 模块](#tab/az)

此示例使用 **Az** PowerShell cmdlet 和存储在 GitHub 上的模板。 此模板创建 Windows Server 2012 R2 Datacenter 虚拟机。

>[!NOTE]
> 尝试此示例之前，请确保已为 Azure Stack 集线器用户 [配置了 PowerShell](azure-stack-powershell-configure-user.md) 。

1. 浏览 [AzureStack-QuickStart-Templates 存储库](https://aka.ms/AzureStackGitHub)，找到 **101-simple-windows-vm** 模板。 将模板保存到此位置：`C:\templates\azuredeploy-101-simple-windows-vm.json`。
2. 打开权限提升的 PowerShell 命令提示符。
3. 将以下脚本中的 `username` 和 `password` 替换为你的用户名和密码，然后运行脚本：

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > 每次运行此脚本时，都应递增 `$myNum` 参数的值，以避免覆盖你的部署。

4. 打开 Azure Stack 集线器门户，选择 " **浏览**"，然后选择 "  **虚拟机** " 以查找新虚拟机 (**myDeployment001**) 。

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm)

此示例使用 **AzureRM** PowerShell cmdlet 和存储在 GitHub 上的模板。 此模板创建 Windows Server 2012 R2 Datacenter 虚拟机。

>[!NOTE]
> 尝试此示例之前，请确保已为 Azure Stack 集线器用户 [配置了 PowerShell](azure-stack-powershell-configure-user.md) 。

1. 浏览 [AzureStack-QuickStart-Templates 存储库](https://aka.ms/AzureStackGitHub)，找到 **101-simple-windows-vm** 模板。 将模板保存到此位置：`C:\templates\azuredeploy-101-simple-windows-vm.json`。
2. 打开权限提升的 PowerShell 命令提示符。
3. 将以下脚本中的 `username` 和 `password` 替换为你的用户名和密码，然后运行脚本：

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRMResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRMResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > 每次运行此脚本时，都应递增 `$myNum` 参数的值，以避免覆盖你的部署。

4. 打开 Azure Stack 集线器门户，选择 " **浏览**"，然后选择 "  **虚拟机** " 以查找新虚拟机 (**myDeployment001**) 。

---
## <a name="cancel-a-running-template-deployment"></a>取消正在运行的模板部署

若要取消正在运行的模板部署，请使用 `Stop-AzResourceGroupDeployment` PowerShell cmdlet。

## <a name="next-steps"></a>后续步骤

- [使用 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
