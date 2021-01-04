---
title: 使用 PowerShell 连接到 Azure Stack Hub
description: 了解如何使用 PowerShell 连接到 Azure Stack Hub。
author: mattbriggs
ms.topic: article
ms.date: 11/19/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 791923d3b2deacdc886dfcc0167736bd7214f9b8
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697625"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>使用 PowerShell 连接到 Azure Stack Hub

可以将 Azure Stack Hub 配置为使用 PowerShell 来管理资源，例如创建套餐、计划、配额以及警报。 本主题有助于配置操作员环境

## <a name="prerequisites"></a>先决条件

如果[已通过 VPN 连接到 ASDK](../asdk/asdk-connect.md#connect-with-vpn)，请通过 [Azure Stack 开发工具包 (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) 或基于 Windows 的外部客户端运行以下先决条件操作。

- 安装 [Azure Stack Hub 兼容的 Azure PowerShell 模块](powershell-install-az-module.md)。  
- 下载[使用 Azure Stack Hub 所需的工具](azure-stack-powershell-download.md)。  

## <a name="connect-with-azure-ad"></a>与 Azure AD 连接

若要使用 PowerShell 配置 Azure Stack Hub 操作员环境，请运行以下脚本之一。 将 Azure Active Directory (Azure AD) tenantName 和 Azure 资源管理器终结点值替换为你自己的环境配置。

### <a name="az-modules"></a>[Az 模块](#tab/az1)

[!include[Remove Account](../includes/remove-account-az.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

[!include[Remove Account](../includes/remove-account-azurerm.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

---


## <a name="connect-with-ad-fs"></a>与 AD FS 连接

使用 PowerShell 通过 Active Directory 联合身份验证服务 (Azure AD FS) 连接到 Azure Stack Hub 操作员环境。 对于 ASDK，此 Azure 资源管理器终结点设置为 `https://adminmanagement.local.azurestack.external`。 若要获取 Azure Stack Hub 集成系统的 Azure 资源管理器终结点，请与服务提供商联系。

### <a name="az-modules"></a>[Az 模块](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzAccount -EnvironmentName "AzureStackAdmin"
  ```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)

```powershell  
# Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
    -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
    -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

# Sign in to your environment.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
```

---

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，请使用 PowerShell 在 Azure Stack Hub 中创建资源。 例如，可以为应用创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组。

### <a name="az-modules"></a>[Az 模块](#tab/az3)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm3)

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 管理 Azure Stack 中心的订阅、计划和产品/服务](azure-stack-powershell-plan-offer.md)
- [为 Azure Stack Hub 开发模板](../user/azure-stack-develop-templates.md)。
- [使用 PowerShell 部署模板](../user/azure-stack-deploy-template-powershell.md)。
- [Azure Stack Hub 模块参考](/powershell/azure/azure-stack/overview)。
