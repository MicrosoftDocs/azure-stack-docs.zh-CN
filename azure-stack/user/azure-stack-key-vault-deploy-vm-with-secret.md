---
title: 使用 Key Vault 中存储的密码部署 Azure Stack Hub VM
description: 了解如何使用 Azure Stack Hub 密钥保管库中存储的密码部署 VM。
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 7c68c2b5ecc5a56449e67dd0ffa403b3363cfab9
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77702884"
---
# <a name="deploy-an-azure-stack-hub-vm-using-a-password-stored-in-key-vault"></a>使用 Key Vault 中存储的密码部署 Azure Stack Hub VM

本文介绍如何使用 Azure Stack Hub 密钥保管库中存储的密码部署 Windows Server 虚拟机 (VM)。 使用密钥保管库密码比传递纯文本密码更安全。

## <a name="overview"></a>概述

可以将密码等值作为机密存储在 Azure Stack Hub 密钥保管库中。 创建机密后，可以在 Azure 资源管理器模板中引用它。 通过资源管理器使用机密提供以下好处：

* 每次部署资源时不必手动输入机密。
* 可以指定哪些用户或服务主体可以访问机密。

## <a name="prerequisites"></a>必备条件

* 必须订阅包含 Key Vault 服务的产品/服务。
* [安装适用于 Azure Stack Hub 的 PowerShell](../operator/azure-stack-powershell-install.md)。
* [配置 PowerShell 环境。](azure-stack-powershell-configure-user.md)

以下步骤说明通过检索 Key Vault 中存储的密码创建 VM 所需的过程：

1. 创建 Key Vault 机密。
2. 更新 `azuredeploy.parameters.json` 文件。
3. 部署模板。

> [!NOTE]  
> 可以通过 Azure Stack 开发工具包 (ASDK) 或者外部客户端（如果已通过 VPN 建立连接）执行这些步骤。

## <a name="create-a-key-vault-secret"></a>创建 Key Vault 机密

以下脚本创建密钥保管库，并将密码作为机密存储在密钥保管库中。 创建密钥保管库时，请使用 `-EnabledForDeployment` 参数。 此参数可确保能够从 Azure 资源管理器模板引用密钥保管库。

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

运行前面的脚本时，输出会包括机密 URI（统一资源标识符）。 请记下此 URI。 在[使用密钥保管库中的密码部署 Windows VM](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) 模板中，需要引用此 URI。 将 [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) 文件夹下载到开发计算机上。 此文件夹包含 `azuredeploy.json` 和 `azuredeploy.parameters.json` 文件，在后续步骤中将需要这些文件。

根据环境值，修改 `azuredeploy.parameters.json` 文件。 要注意的参数是保管库名称、保管库资源组和机密 URI（由前面的脚本生成）。 以下文件是参数文件的示例。

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.parameters.json 文件

根据环境，以 KeyVault URI、secretName、VM 的 adminUsername 值更新 `azuredeploy.parameters.json` 文件。 以下 JSON 文件显示模板参数文件的示例：

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
