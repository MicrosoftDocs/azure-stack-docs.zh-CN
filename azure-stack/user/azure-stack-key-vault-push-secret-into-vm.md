---
title: 在 Azure Stack 中心部署具有安全存储的证书的 VM |Microsoft Docs
description: 了解如何在 Azure Stack 集线器中使用密钥保管库部署虚拟机并将证书推送到该虚拟机
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: e393f0142f403e175427de87738db1a2bf64233a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820315"
---
# <a name="deploy-a-vm-with-a-securely-stored-certificate-on-azure-stack-hub"></a>在 Azure Stack 中心部署具有安全存储的证书的 VM 

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

本文介绍如何部署安装了 Key Vault 证书的 Azure Stack 中心虚拟机（VM）。

## <a name="overview"></a>概述

在许多情况下使用证书，如对 Active Directory 进行身份验证或加密 web 流量。 可以安全地将证书存储为 Azure Stack 中心密钥保管库中的机密。 使用 Azure Stack 中心 Key Vault 的优点是：

* 不会在脚本、命令行历史记录或模板中公开证书。
* 证书管理过程得到了简化。
* 可以控制访问证书的密钥。

## <a name="process-description"></a>进程说明

以下步骤描述了将证书推送到 VM 所需的过程：

1. 创建密钥保管库机密。
2. 更新**azuredeploy.json**文件。
3. 部署模板。

> [!NOTE]
> 如果通过 VPN 进行连接，则可以从 Azure Stack 开发工具包（ASDK）或从外部客户端使用这些步骤。

## <a name="prerequisites"></a>必备组件

* 你必须订阅包含 Key Vault 服务的产品/服务。
* [为 Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)。
* [配置 Azure Stack 集线器用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

## <a name="create-a-key-vault-secret"></a>创建密钥保管库机密

下面的脚本创建一个采用 .pfx 格式的证书，创建一个密钥保管库，并将该证书作为机密存储在密钥保管库中。

> [!IMPORTANT]
> 创建密钥保管库时，必须使用 `-EnabledForDeployment` 参数。 此参数可确保从 Azure 资源管理器模板引用密钥保管库。

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<certificate thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path to where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path to where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

运行此脚本时，输出包含机密 URI。 记下此 URI，因为你必须在将[证书推送到 Windows 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)中引用它。 将 " [vm-推送证书-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)模板" 文件夹下载到你的开发计算机。 此文件夹包含**azuredeploy.json**和**azuredeploy.json**文件，需要在以下步骤中执行此操作。

根据你的环境值修改**azuredeploy.json**文件。 重要参数是保管库名称、保管库资源组和机密 URI （由上一个脚本生成）。 以下部分显示了一个参数文件示例。

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.json 文件

根据你的环境，根据你的环境更新 `vaultName`、机密 URI、`VmName`和其他参数的**azuredeploy.json**文件。 下面的 JSON 文件显示模板参数文件的示例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>部署模板

使用以下 PowerShell 脚本部署模板：

```powershell
# Deploy a Resource Manager template to create a VM and push the secret to it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

成功部署模板后，将显示以下输出：

![模板部署结果](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

在部署过程中，Azure Stack 中心将证书推送到 VM。 证书位置取决于 VM 的操作系统：

* 在 Windows 中，证书将添加到**LocalMachine**证书位置，并附带用户提供的证书存储区。
* 在 Linux 中，证书放置在 **/var/lib/waagent**目录下，文件名为**UppercaseThumbprint** ，表示 X509 证书文件， **UppercaseThumbprint prv**用于私钥。

## <a name="retire-certificates"></a>停用证书

停用证书是证书管理过程的一部分。 你无法删除旧版本的证书，但你可以通过使用 `Set-AzureKeyVaultSecretAttribute` cmdlet 来禁用它。

下面的示例演示如何禁用证书。 对 `VaultName`、`Name`和 `Version` 参数使用自己的值。

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>后续步骤

* [使用密钥保管库密码部署 VM](azure-stack-key-vault-deploy-vm-with-secret.md)
* [允许应用程序访问 Key Vault](azure-stack-key-vault-sample-app.md)
