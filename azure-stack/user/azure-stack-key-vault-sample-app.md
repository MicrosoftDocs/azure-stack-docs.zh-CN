---
title: 允许应用访问 Azure Stack 中心 Key Vault 机密
description: 了解如何在 Azure Stack Hub 中运行从密钥保管库检索密钥和机密的示例应用。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 4db38de1586096cfeeb2e7f2b806430d0ca1344f
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492302"
---
# <a name="allow-apps-to-access-azure-stack-hub-key-vault-secrets"></a>允许应用访问 Azure Stack 中心 Key Vault 机密

请按照本文中的步骤运行示例应用程序， **HelloKeyVault**从 Azure Stack 集线器中的密钥保管库检索密钥和机密。

## <a name="prerequisites"></a>必备条件

如果[通过 VPN 进行连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)，则可以从[Azure Stack 开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)或基于 Windows 的外部客户端安装以下系统必备组件：

* 安装[Azure Stack 集线器兼容的 Azure PowerShell 模块](../operator/azure-stack-powershell-install.md)。
* 下载[处理 Azure Stack 中心所需的工具](../operator/azure-stack-powershell-download.md)。

## <a name="create-a-key-vault-and-register-an-app"></a>创建密钥保管库并注册应用

为示例应用程序做好准备：

* 在 Azure Stack Hub 中创建密钥保管库。
* 在 Azure Active Directory （Azure AD）中注册应用。

使用 Azure 门户或 PowerShell 来准备示例应用。

> [!NOTE]
> 默认情况下，PowerShell 脚本在 Active Directory 中创建一个新应用程序。 但是，你可以注册一个现有的应用程序。

运行以下脚本之前，请确保为 `aadTenantName` 和 `applicationPassword` 变量提供值。 如果未指定 `applicationPassword`的值，则此脚本会生成一个随机密码。

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
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

下图显示了用于创建密钥保管库的脚本的输出：

![具有访问密钥的密钥保管库](media/azure-stack-key-vault-sample-app/settingsoutput.png)

记下前面脚本返回的**VaultUrl**、 **AuthClientId**和**AuthClientSecret**值。 使用这些值来运行**HelloKeyVault**应用程序。

## <a name="download-and-configure-the-sample-application"></a>下载并配置示例应用程序

从 Azure [Key Vault 客户端示例](https://www.microsoft.com/download/details.aspx?id=45343)页下载密钥保管库示例。 提取开发工作站上 .zip 文件的内容。 Samples 文件夹中有两个应用。 本文使用**HelloKeyVault**。

加载**HelloKeyVault**示例：

1. 浏览到**KeyVault** > **示例** > **HelloKeyVault**文件夹。
2. 在 Visual Studio 中打开**HelloKeyVault**应用。

### <a name="configure-the-sample-application"></a>配置示例应用程序

在 Visual Studio 中：

1. 打开 HelloKeyVault\App.config 文件并查找 `<appSettings>` 元素。
2. 用创建密钥保管库时返回的值更新**VaultUrl**、 **AuthClientId**和**AuthCertThumbprint**键。 默认情况下，App.config 文件的 `AuthCertThumbprint`占位符。 将此占位符替换 `AuthClientSecret`。

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

## <a name="run-the-app"></a>运行应用

当你运行**HelloKeyVault**时，应用程序会登录 Azure AD，然后使用 `AuthClientSecret` 令牌向 Azure Stack 集线器中的密钥保管库进行身份验证。

可以使用**HelloKeyVault**示例执行以下操作：

* 对密钥和机密执行基本操作，例如创建、加密、包装和删除。
* 将 `encrypt` 和 `decrypt` 等参数传递到**HelloKeyVault**，并将指定更改应用到密钥保管库。

## <a name="next-steps"></a>后续步骤

* [使用密钥保管库密码部署 VM](azure-stack-key-vault-deploy-vm-with-secret.md)
* [使用 Key Vault 证书部署 VM](azure-stack-key-vault-push-secret-into-vm.md)
