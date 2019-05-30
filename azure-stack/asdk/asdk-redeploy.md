---
title: 重新部署 Azure Stack 开发工具包 (ASDK) | Microsoft Docs
description: 本文介绍如何重新安装 ASDK。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f61fff0d29b1e0bf847ffc1761ff53c90b703991
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267777"
---
# <a name="redeploy-the-asdk"></a>重新部署 ASDK
本文介绍如何在非生产环境中重新部署 Azure Stack 开发工具包 (ASDK)。 由于不支持 ASDK 升级，因此若要改用较新的版本，需彻底地进行重新部署。 也可随时根据需要从头开始重新部署 ASDK。

> [!IMPORTANT]
> 不支持将 ASDK 升级到新的版本。 每次需要对较新版的 Azure Stack 进行评估时，需在开发工具包主机上重新部署 ASDK。

## <a name="remove-azure-registration"></a>删除 Azure 注册 
如果以前已将 ASDK 安装注册到 Azure，则应在重新部署 ASDK 之前删除注册资源。 重新部署 ASDK 时，请重新注册 ASDK 以使市场中可以提供项目。 如果以前未将 ASDK 注册到 Azure 订阅，则可跳过此部分。

若要删除注册资源，请使用 **Remove-AzsRegistration** cmdlet 注销 Azure Stack。 然后，使用 **Remove-AzureRMResourceGroup** cmdlet 从 Azure 订阅中删除 Azure Stack 资源组：

1. 在能够访问特权终结点的计算机上以管理员身份打开 PowerShell 控制台。 对于 ASDK 来说，该计算机是开发工具包主机。

2. 运行以下 PowerShell 命令，注销 ASDK 安装并从 Azure 订阅中删除 **azurestack** 资源组：

   ```powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. 当脚本运行时，系统会提示你登录 Azure 订阅和本地 ASDK 安装。
4. 脚本完成后，会看到与以下示例类似的消息：

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



此时 Azure Stack 应该会成功地从 Azure 订阅注销。 另外也会删除在将 ASDK 注册到 Azure 时创建的 azurestack 资源组。

## <a name="deploy-the-asdk"></a>部署 ASDK
若要重新部署 Azure Stack，必须从头开始进行，如下所述。 这些步骤可能会有所不同，具体取决于是否使用了 Azure Stack 安装程序 (asdk-installer.ps1) 脚本来安装 ASDK。

### <a name="redeploy-the-asdk-using-the-installer-script"></a>使用安装程序脚本重新部署 ASDK
1. 在 ASDK 计算机上打开提升了权限的 PowerShell 控制台，导航到非系统驱动器上 **AzureStack_Installer** 目录中的 asdk-installer.ps1 脚本。 运行脚本，然后单击“重启”。 

   ![运行 asdk-installer.ps1 脚本](media/asdk-redeploy/1.png)

2. 选择基础操作系统（非 **Azure Stack**），然后单击“下一步”  。

   ![重启到主机操作系统中](media/asdk-redeploy/2.png)

3. 在开发工具包主机重启到基本操作系统中以后，以本地管理员身份登录。 找到并删除先前的部署过程中使用过的 **C:\CloudBuilder.vhdx** 文件。 

4. 重复首次[部署 ASDK](asdk-install.md) 时执行过的步骤。

### <a name="redeploy-the-asdk-without-using-the-installer"></a>在不使用安装程序的情况下重新部署 ASDK
如果未使用 asdk-installer.ps1 脚本来安装 ASDK，必须以手动方式重新配置开发工具包主机，然后才能重新部署 ASDK。

1. 在 ASDK 计算机上运行 **msconfig.exe**，以便启动系统配置实用程序。 在“启动”选项卡上选择主机操作系统（不是 Azure Stack），单击“设置为默认值”，然后单击“确定”。    出现提示时单击“重启”  。

      ![设置启动配置](media/asdk-redeploy/4.png)

2. 在开发工具包主机重启到基本操作系统中以后，以开发工具包主机本地管理员身份登录。 找到并删除先前的部署过程中使用过的 **C:\CloudBuilder.vhdx** 文件。 

3. 重复首次[使用 PowerShell 部署 ASDK](asdk-deploy-powershell.md) 时执行过的步骤。


## <a name="next-steps"></a>后续步骤
[ASDK 后部署任务](asdk-post-deploy.md)




