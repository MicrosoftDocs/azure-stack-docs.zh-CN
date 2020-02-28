---
title: 在 Azure Stack 集线器中使用 PowerShell 部署模板
description: 在 Azure Stack 集线器中使用 PowerShell 部署模板。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 9081464e05825cbd211f043818e841276a840303
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704261"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>在 Azure Stack 集线器中使用 Powershell 部署模板

可以使用 PowerShell 将 Azure 资源管理器模板部署到 Azure Stack 中心。 本文介绍如何使用 PowerShell 部署模板。

## <a name="run-azurerm-powershell-cmdlets"></a>运行 AzureRM PowerShell cmdlet

此示例使用**AzureRM** PowerShell Cmdlet 和 GitHub 上存储的模板。 模板创建 Windows Server 2012 R2 Datacenter 虚拟机。

>[!NOTE]
> 尝试此示例之前，请确保已为 Azure Stack 集线器用户[配置了 PowerShell](azure-stack-powershell-configure-user.md) 。

1. 浏览[test-azurestack](https://aka.ms/AzureStackGitHub)存储库，并找到 101-simple- **vm**模板。 将模板保存到以下位置： `C:\templates\azuredeploy-101-simple-windows-vm.json`。
2. 打开权限提升的 PowerShell 命令提示符。
3. 将以下脚本中的 `username` 和 `password` 替换为你的用户名和密码，然后运行该脚本：

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > 每次运行此脚本时，将递增 `$myNum` 参数的值，以防止覆盖你的部署。

4. 打开 Azure Stack 集线器门户，选择 "**浏览**"，然后选择 "**虚拟机**" 以查找新虚拟机（**myDeployment001**）。

## <a name="cancel-a-running-template-deployment"></a>取消正在运行的模板部署

若要取消正在运行的模板部署，请使用 `Stop-AzureRmResourceGroupDeployment` PowerShell cmdlet。

## <a name="next-steps"></a>后续步骤

- [使用 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
