---
title: 使用存储在 Key Vault 中的密码部署 Azure Stack 中心 VM
description: 了解如何使用 Azure Stack 中心密钥保管库中存储的密码来部署 VM。
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 7c68c2b5ecc5a56449e67dd0ffa403b3363cfab9
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702884"
---
# <a name="deploy-an-azure-stack-hub-vm-using-a-password-stored-in-key-vault"></a>使用存储在 Key Vault 中的密码部署 Azure Stack 中心 VM

本文逐步介绍如何使用 Azure Stack 中心 Key Vault 中存储的密码部署 Windows Server 虚拟机（VM）。 使用密钥保管库密码比传递纯文本密码更安全。

## <a name="overview"></a>概述

可以在 Azure Stack 中心密钥保管库中存储值（如密码）作为机密。 创建机密后，可以在 Azure 资源管理器模板中引用它。 结合使用机密与资源管理器具有以下优势：

* 不需要在每次部署资源时都手动输入密码。
* 可以指定哪些用户或服务主体可以访问机密。

## <a name="prerequisites"></a>必备条件

* 你必须订阅包含 Key Vault 服务的产品/服务。
* [为 Azure Stack 集线器安装 PowerShell。](../operator/azure-stack-powershell-install.md)
* [配置你的 PowerShell 环境。](azure-stack-powershell-configure-user.md)

以下步骤描述了通过检索存储在 Key Vault 中的密码创建 VM 所需的过程：

1. 创建 Key Vault 的机密。
2. 更新 `azuredeploy.parameters.json` 文件。
3. 部署模板。

> [!NOTE]  
> 如果通过 VPN 进行连接，则可以从 Azure Stack 开发工具包（ASDK）或从外部客户端使用这些步骤。

## <a name="create-a-key-vault-secret"></a>创建 Key Vault 机密

以下脚本将创建一个密钥保管库，并将密钥保管库中的密码存储为机密。 创建密钥保管库时，请使用 `-EnabledForDeployment` 参数。 此参数可确保从 Azure 资源管理器模板引用密钥保管库。

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

运行前面的脚本时，输出包含机密 URI （统一资源标识符）。 记下此 URI。 必须在 "[使用密钥保管库中的密码部署 WINDOWS VM](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) " 模板中引用它。 将[101-vm 安全密码](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)文件夹下载到开发计算机上。 此文件夹包含 `azuredeploy.json` 和 `azuredeploy.parameters.json` 文件，后续步骤中将需要这些文件。

根据环境值修改 `azuredeploy.parameters.json` 文件。 特别感兴趣的参数是保管库名称、保管库资源组和机密 URI （由上一个脚本生成）。 下面的文件是一个参数文件示例。

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.json 文件

根据你的环境，用 VM 值的 KeyVault URI，secretName，adminUsername 更新 `azuredeploy.parameters.json` 文件。 下面的 JSON 文件显示模板参数文件的示例：

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
       "adminPassword":  {
         "reference":  {
            "keyVault":  {
              "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
              },
            "secretName":  "MySecret"
         }
       },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>模板部署

现在，使用以下 PowerShell 脚本部署模板：

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

成功部署模板后，会生成以下输出：

![部署输出](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>后续步骤

* [使用 Key Vault 部署示例应用](azure-stack-key-vault-sample-app.md)
* [使用 Key Vault 证书部署 VM](azure-stack-key-vault-push-secret-into-vm.md)
