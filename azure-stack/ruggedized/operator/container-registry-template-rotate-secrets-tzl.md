---
title: 在 Azure Stack Hub 中旋转容器注册表机密 |Microsoft Docs
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 中旋转容器注册表机密。
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
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 60caf869fef5537aa6adc35bd2233a81a4fe749d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909814"
---
# <a name="rotate-container-registry-secrets-in-azure-stack-hub"></a>在 Azure Stack 集线器中旋转容器注册表机密

Azure Stack 中心用户可以为容器注册表模板部署轮换 (证书、用户名和密码) 的机密。 您可以运行脚本，以 Microsoft Azure Key Vault 中填充新的机密值，并重新 **部署** 现有的容器注册表模板实例。 轮换机密不需要新的部署。

## <a name="prerequisites-for-the-user"></a>用户的先决条件

 - 用户需要安装 Azure Stack 中心 Powershell 模块。 有关详细信息，请参阅[安装适用于 Azure Stack 的 PowerShell](../../operator/azure-stack-powershell-install.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fbreadcrumb%2Ftoc.json)。

 - 获取容器注册表模板的更新密码。 可以使用新的 SSL 证书或新的用户名和密码组合来访问 Docker 注册表。

 - `\registry\scripts`从[msazurestackworkloads/test-azurestack-库](https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip)GitHub 存储库下载 zip 文件后，获取中的脚本。

## <a name="import-new-secrets-into-key-vault"></a>导入新的机密 Key Vault

按照以下说明在 Key Vault 中设置新机密。

### <a name="set-updated-registry-user-password-for-existing-username"></a>为现有用户名设置更新的注册表用户密码

1.  打开提升权限的 PowerShell 提示符，然后 `Import-Module .\\pre-reqs.ps1` 从 "脚本" 文件夹运行。

2.  若要更新现有注册表用户的值，请运行以下 cmdlet：

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <username> `
        -RegistryUserPassword <newpassword> `
        -SkipExistCheck $true
    ```
    例如，该 cmdlet 将返回以下输出：

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin `
        -RegistryUserPassword password1 `
        -SkipExistCheck $true 
    
    Check if key vault secret name (admin) exists.
    Creating key vault secret name (admin) as it does not exist.
    ```

1.  若要验证是否已为此记录输入新值，请打开提升的 PowerShell 提示符并运行以下 cmdlet：

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    ```
    例如，该 cmdlet 将返回以下输出：
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 2a1495372c474cc890c888518f02b19f
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/2a1495372c474cc890c888518f02b19f
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 7:05:56 PM
    Updated      : 12/18/2019 7:05:56 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 3fd65c1719c74997984648de18a1fa0e
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/3fd65c1719c74997984648de18a1fa0e
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:05:56 AM
    Updated      : 12/17/2019 5:05:56 AM
    Content Type : user credentials
    Tags         : 
    ```

### <a name="set-new-registry-username-and-password"></a>设置新的注册表用户名和密码

1.  `Import-Module .\pre-reqs.ps1`从 "脚本" 文件夹中打开提升的 PowerShell 提示符。

2.  若要为新的用户名和密码创建新的机密，请打开提升的 PowerShell 提示符并运行以下 cmdlet：

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <newusername> `
        -RegistryUserPassword <newpassword> 
    ```
    例如，该 cmdlet 将返回以下输出：
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin1 `
        -RegistryUserPassword password1
    
    Check if key vault secret name (admin1) exists.
    Creating key vault secret name (admin1) as it does not exist. 
    ```

1.  若要验证是否已创建新机密，请打开提升的 PowerShell 提示符并运行以下 cmdlet：

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName \<KeyVaultName> -Name \<username>
    ```

    例如，该 cmdlet 将返回以下输出：

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin1
    
    
    Vault Name   : newregkv
    Name         : admin1
    Version      : 2ae9a7239f4044be82ca9d1e9b80e85a
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/admin1/2ae9a7239f4044be82ca9d1e9b80e85a
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:28:18 PM
    Updated      : 12/18/2019 11:28:18 PM
    Content Type : user credentials
    Tags         : 
    ```

> [!Important]  
> 如果要创建新机密 (用户名/密码组合) 你将需要删除旧 Key Vault 机密。 如果在不删除旧密码的情况下重新部署现有容器注册表模板，则新旧的用户名和密码组合将对登录注册表有效。

### <a name="update-the-ssl-certificate-for-existing-key-vault-secret"></a>更新现有 Key Vault 密钥的 SSL 证书

1. 打开提升权限的 PowerShell 提示符并运行以下 cmdlet：

    ```powershell
    Set-CertificateSecret -KeyVaultName \<keyvaultname> `
     -CertificateSecretName \<originalsecretnameforcertificate> `
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <originalsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    ```

    例如，该 cmdlet 将返回以下输出：

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    Check if key vault secret name (containersecret) exists.
    Creating key vault secret name (containersecret) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourceGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61
    ---------------------------------------------------------------- 
    ```

1.  重新部署现有容器注册表模板时，你将使用此函数生成的值。

2.  若要验证是否已创建新版本的现有机密，请打开提升的 PowerShell 提示符并运行以下 cmdlet：

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName <KeyVaultName> -Name <secretname>
    ```

    例如，该 cmdlet 将返回以下输出：

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name containersecret -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : a07ece6b9914408e8f20c516e15b66c9
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:46:28 PM
    Updated      : 12/18/2019 11:46:28 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : 0199c7ec1d8d41bb9ddff0f39dca9931
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/0199c7ec1d8d41bb9ddff0f39dca9931
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:06:03 AM
    Updated      : 12/17/2019 5:06:03 AM
    Content Type : pfx
    Tags         : 
    ```

### <a name="set-a-new-ssl-certificate-for-the-container-registry-template"></a>为容器注册表模板设置新的 SSL 证书

1.  打开提升权限的 PowerShell 提示符，并运行以下 cmdlet：

    ```powershell  
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <newsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword>
    ```

    例如，该 cmdlet 将返回以下输出：

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts>    Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret121719 `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> 
    Check if key vault secret name (containersecret121719) exists.
    Creating key vault secret name (containersecret121719) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourc
    eGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.c
    om:443/secrets/containersecret121719/bb2cfe4df7bc4fbe854a00799afa8566
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61 
    ```

## <a name="redeploy-existing-container-registry-template"></a>重新部署现有容器注册表模板

1. 打开 Azure Stack Hub 用户门户。

2.  导航到容器注册表模板 VM 所部署到的资源组。

    ![显示容器注册表模板 VM 所部署到的资源组的屏幕截图。](./media/container-registry-template-rotating-secrets-tzl/image1.png)

3. 选择 **部署下的部署。**

    ![显示在 "部署" 页上选择的部署的屏幕截图。](./media/container-registry-template-rotating-secrets-tzl/image2.png)

4.  如果是第一次轮换密钥，请选择原始部署。 如果这不是第一次轮换机密，请选择最新部署，然后选择 "重新 **部署**"。

    ![显示模板 "概述" 页面并突出显示 "重新部署" 操作的屏幕截图。](./media/container-registry-template-rotating-secrets-tzl/image3.png)

5.  在 " **部署解决方案模板**" 中，选择 " **使用现有资源组** "，并选择最初用于部署容器注册表模板的资源组。 为了成功重新部署，必须使用相同的资源组。

    ![显示 "部署解决方案模板" 和 "参数" 页的屏幕截图。](./media/container-registry-template-rotating-secrets-tzl/image4.png)

6.  In **参数** 检查参数是否与原始部署匹配。 将需要添加服务主体客户端 ID 和服务主体机密。

    - 如果只是轮换注册表服务的用户名和密码，只需添加服务主体参数。

    - 如果要轮换此证书，则需要输入 PFXKeyVaultSecretURL 和 PFXThumbprint 的新值，这些值是通过设置新机密输出的。

    ![容器注册表模板](./media/container-registry-template-rotating-secrets-tzl/image5.png)

7.  选择“确定”，然后选择“创建”。 重新部署将继续。 在重新部署过程中，注册表功能将继续工作。

    - 如果要轮换用户名和密码，重新部署完成后，将需要再次向注册表进行身份验证。

    - 如果要旋转证书，则不会遇到对注册表的任何访问权限。 这假定你使用的是受信任的证书提供程序中的证书。 如果使用私有证书，则需要在客户端上安装此证书，以防止访问丢失。

## <a name="next-steps"></a>后续步骤

[Azure Stack 市场概述](../../operator/azure-stack-marketplace.md)
