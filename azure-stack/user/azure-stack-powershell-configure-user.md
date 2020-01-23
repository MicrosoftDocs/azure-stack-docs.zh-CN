---
title: 使用 PowerShell 以用户身份连接到 Azure Stack 集线器 |Microsoft Docs
description: 了解如何通过 PowerShell 连接到 Azure Stack 集线器。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 98b5cad057ae8a231569cfa7348cb9df16591ff4
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536022"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>使用 PowerShell 以用户身份连接到 Azure Stack 集线器

可以通过 PowerShell 连接到 Azure Stack 集线器，以管理 Azure Stack 中心资源。 例如，你可以使用 PowerShell 订阅产品/服务、创建虚拟机（Vm）以及部署 Azure 资源管理器模板。

若要获取安装程序：
  - 确保满足要求。
  - 与 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 连接。 
  - 注册资源提供程序。
  - 测试连接。

## <a name="prerequisites-to-connecting-with-powershell"></a>与 PowerShell 连接的先决条件

如果[通过 VPN 进行连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)，请从[开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)或基于 Windows 的外部客户端配置这些先决条件：

* 安装[Azure Stack 集线器兼容的 Azure PowerShell 模块](../operator/azure-stack-powershell-install.md)。
* 下载[处理 Azure Stack 中心所需的工具](../operator/azure-stack-powershell-download.md)。

请确保将以下脚本变量替换为 Azure Stack 中心配置中的值：

- **Azure AD 租户名称**  
  用于管理 Azure Stack 中心的 Azure AD 租户的名称。 例如，yourdirectory.onmicrosoft.com。
- **Azure 资源管理器终结点**  
  对于 Azure Stack 开发工具包，此值设置为 https://management.local.azurestack.external 。 若要获取 Azure Stack 集线器集成系统的此值，请与服务提供商联系。

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>通过 Azure AD 连接到 Azure Stack 集线器

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>通过 AD FS 连接到 Azure Stack 集线器

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>注册资源提供程序

资源提供程序不会自动注册到没有通过门户部署任何资源的新用户订阅。 可以通过运行以下脚本显式注册资源提供程序：

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，通过使用 PowerShell 在 Azure Stack Hub 中创建资源来测试连接性。 作为测试，为应用程序创建资源组并添加 VM。 运行以下命令创建名为“MyResourceGroup”的资源组：

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤

- [为 Azure Stack 集线器开发模板](azure-stack-develop-templates.md)
- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
- [Azure Stack 中心 PowerShell 模块参考](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- 若要为云运营商环境设置 PowerShell，请参阅[配置 Azure Stack 集线器操作员的 powershell 环境](../operator/azure-stack-powershell-configure-admin.md)一文。
