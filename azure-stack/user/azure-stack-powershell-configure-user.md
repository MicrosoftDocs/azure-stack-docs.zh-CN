---
title: 以用户身份使用 PowerShell 连接到 Azure Stack Hub
description: 了解如何使用 PowerShell 连接到 Azure Stack 集线器以使用交互式提示符或编写脚本。
author: mattbriggs
ms.topic: article
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: bc0eb1beeec831dee64f300aba4d977546a00058
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518256"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>以用户身份使用 PowerShell 连接到 Azure Stack Hub

可以使用 PowerShell 连接到 Azure Stack Hub 以管理 Azure Stack Hub 资源。 例如，可以使用 PowerShell 来订阅产品/服务、创建虚拟机 (VM) 和部署 Azure 资源管理器模板。

若要进行设置，请执行以下操作：
  - 确保满足要求。
  - 与 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 连接。 
  - 注册资源提供程序。
  - 测试连接。

## <a name="prerequisites-to-connecting-with-powershell"></a>使用 PowerShell 进行连接的先决条件

如果已[通过 VPN 建立连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)，请通过[开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)或基于 Windows 的外部客户端配置这些先决条件：

* 安装 [Azure Stack Hub 兼容的 Azure PowerShell 模块](../operator/powershell-install-az-module.md)。
* 下载[使用 Azure Stack Hub 所需的工具](../operator/azure-stack-powershell-download.md)。

确保将以下脚本变量替换为 Azure Stack Hub 配置中的值：

- **Azure AD 租户名称**  
  用于管理 Azure Stack Hub 的 Azure AD 租户名称。 例如，yourdirectory.onmicrosoft.com。
- **Azure 资源管理器终结点**  
  对于 Azure Stack 开发工具包，此值设置为 `https://management.local.azurestack.external` 。 若要为 Azure Stack Hub 集成系统获取此值，请与服务提供商联系。

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>使用 Azure AD 连接到 Azure Stack Hub

### <a name="az-modules"></a>[Az 模块](#tab/az1)

```powershell  
    Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)
 
```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRMEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRMAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

---


## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>使用 AD FS 连接到 Azure Stack Hub

### <a name="az-modules"></a>[Az 模块](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzAccount -EnvironmentName "AzureStackUser"
  ```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)
 
  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRMAccount -EnvironmentName "AzureStackUser"
  ```

---


## <a name="register-resource-providers"></a>注册资源提供程序

不会自动为没有通过门户部署任何资源的新用户订阅自动注册资源提供程序。 可以通过运行以下脚本显式注册资源提供程序：

### <a name="az-modules"></a>[Az 模块](#tab/az3)

```powershell  
foreach($s in (Get-AzSubscription)) {
        Select-AzSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzResourceProvider -ListAvailable | Register-AzResourceProvider
    }
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm3)
 
```powershell  
foreach($s in (Get-AzureRMSubscription)) {
        Select-AzureRMSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRMResourceProvider -ListAvailable | Register-AzureRMResourceProvider
    }
```

---


[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，通过使用 PowerShell 在 Azure Stack Hub 中创建资源来测试连接性。 作为测试，为应用程序创建资源组并添加 VM。 运行以下命令创建名为“MyResourceGroup”的资源组：

### <a name="az-modules"></a>[Az 模块](#tab/az4)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm4)
 
```powershell  
New-AzureRMResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>后续步骤

- [为 Azure Stack Hub 开发模板](azure-stack-develop-templates.md)
- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
- [Azure Stack 中心 PowerShell 模块参考](/powershell/azure/azure-stack/overview)
- 若要为云运营商环境设置 PowerShell，请参阅 [配置 Azure Stack 集线器操作员的 powershell 环境](../operator/azure-stack-powershell-configure-admin.md) 一文。
