---
title: 允许应用访问 Azure Stack Hub Key Vault 机密
description: 了解如何运行从 Azure Stack Hub 中的密钥保管库检索密钥和机密的示例应用。
author: sethmanheim
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: b30a99182f1c8c1392ae73ec0e70bc06b35a343f
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518290"
---
# <a name="allow-apps-to-access-azure-stack-hub-key-vault-secrets"></a>允许应用访问 Azure Stack Hub Key Vault 机密

本文中的步骤说明如何运行示例应用 HelloKeyVault，该应用从 Azure Stack Hub 中的密钥保管库检索密钥和机密。

## <a name="prerequisites"></a>先决条件

如果已[通过 VPN 建立连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)，可以从 [Azure Stack 开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)或从基于 Windows 的外部客户端安装以下必备组件：

* 安装 [Azure Stack Hub 兼容的 Azure PowerShell 模块](../operator/powershell-install-az-module.md)。
* 下载[使用 Azure Stack Hub 所需的工具](../operator/azure-stack-powershell-download.md)。

## <a name="create-a-key-vault-and-register-an-app"></a>创建密钥保管库并注册应用

若要准备示例应用程序，请执行以下操作：

* 在 Azure Stack Hub 中创建密钥保管库。
* 在 Azure Active Directory (Azure AD) 中注册应用。

使用 Azure 门户或 PowerShell 来准备示例应用。

> [!NOTE]
> 默认情况下，此 PowerShell 脚本会在 Active Directory 中创建一个新的应用。 不过，你也可以注册现有的某个应用程序。

在运行以下脚本之前，请确保为 `aadTenantName` 和 `applicationPassword` 变量提供值。 如果没有为 `applicationPassword` 指定值，此脚本会生成随机密码。

### <a name="az-modules"></a>[Az 模块](#tab/az)

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack Hub user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack Hub operator's PowerShell environment.
Add-AzEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm)

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack Hub user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack Hub operator's PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRMAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRMADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRMADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRMResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRMKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRMKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

---



下图显示用于创建密钥保管库的脚本的输出：

![具有访问密钥的密钥保管库](media/azure-stack-key-vault-sample-app/settingsoutput.png)

请记下前面脚本返回的 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 值。 将使用这些值运行 **HelloKeyVault** 应用程序。

## <a name="download-and-configure-the-sample-application"></a>下载并配置示例应用程序

从 Azure [密钥保管库客户端示例](https://www.microsoft.com/download/details.aspx?id=45343)页下载密钥保管库示例。 将 .zip 文件的内容解压缩到开发工作站上。 samples 文件夹中有两个应用，本文使用了 HelloKeyVault。

若要加载 **HelloKeyVault** 示例，请执行以下操作：

1. 浏览到 **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault** 文件夹。
2. 在 Visual Studio 中打开 **HelloKeyVault** 应用。

### <a name="configure-the-sample-application"></a>配置示例应用程序

在 Visual Studio 中：

1. 打开 HelloKeyVault\App.config 文件，找到 `<appSettings>` 元素。
2. 使用创建密钥保管库时返回的值更新 **VaultUrl**、**AuthClientId** 和 **AuthCertThumbprint** 密钥。 默认情况下，App.config 文件有一个用于 `AuthCertThumbprint` 的占位符。 请将此占位符替换为 `AuthClientSecret`。

   ```xml
   <appSettings>
    <!-- Update these settings for your test environment -->
    <add key="VaultUrl" value="URL to your Vault" />
    <add key="AuthClientId" value="Client Id of your Service Principal" />
    <add key="AuthCertThumbprint" value="Thumbprint of the certificate used for authentication" />
    <add key="TracingEnabled" value="false" />
   </appSettings>
   ```

3. 重新生成解决方案。

## <a name="run-the-app"></a>运行应用程序

运行 **HelloKeyVault** 时，应用会登录到 Azure AD，然后使用 `AuthClientSecret` 令牌向 Azure Stack Hub 中的密钥保管库进行身份验证。

可以使用 **HelloKeyVault** 示例执行以下操作：

* 对密钥和机密执行基本操作，例如创建、加密、包装和删除。
* 向 **HelloKeyVault** 传递诸如 `encrypt` 和 `decrypt` 之类的参数，以及向密钥保管库应用指定的更改。

## <a name="next-steps"></a>后续步骤

* [使用密钥保管库密码部署 VM](azure-stack-key-vault-deploy-vm-with-secret.md)
* [使用 Key Vault 证书部署 VM](azure-stack-key-vault-push-secret-into-vm.md)
