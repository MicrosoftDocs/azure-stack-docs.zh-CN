---
title: 重新部署 ASDK
description: 了解如何重新部署 Azure Stack 开发工具包（ASDK）。
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: d0fc4539b581474c9db2a2dbb05495c9b1bce695
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873509"
---
# <a name="redeploy-the-asdk"></a>重新部署 ASDK
本文介绍如何在非生产环境中重新部署 Azure Stack 开发工具包（ASDK）。 由于不支持升级 ASDK，因此你需要完全重新部署它才能移到较新的版本。 你还可以在想要从头开始时重新部署 ASDK。

> [!IMPORTANT]
> 不支持将 ASDK 升级到新版本。 每当你想要评估 Azure Stack 的较新版本时，你必须在 ASDK 主机计算机上重新部署 ASDK。

## <a name="remove-azure-registration"></a>删除 Azure 注册 
如果以前已将 ASDK 安装注册到 Azure，则应在重新部署 ASDK 之前删除注册资源。 重新注册 ASDK，以便在重新部署 ASDK 时启用 marketplace 中的项目。 如果尚未向 Azure 订阅注册 ASDK，可以跳过此部分。

若要删除注册资源，请使用**set-azsregistration** cmdlet 注销 Azure Stack。 然后，使用**remove-azurermresourcegroup** Cmdlet 从 Azure 订阅中删除 Azure Stack 资源组：

1. 在可以访问特权终结点的计算机上以管理员身份打开 PowerShell 控制台。 对于 ASDK，这是 ASDK 主机计算机。

2. 运行以下 PowerShell 命令，注销 ASDK 安装并从 Azure 订阅中删除**test-azurestack**资源组：

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

3. 脚本运行时，系统将提示你登录到 Azure 订阅和本地 ASDK 安装。
4. 脚本完成后，应会看到类似于以下示例的消息：

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Azure Stack 现在应从 Azure 订阅中成功注销。 还应删除 test-azurestack 资源组。 此资源组是第一次将 ASDK 注册到 Azure 时创建的资源组。

## <a name="deploy-the-asdk"></a>部署 ASDK
若要重新部署 Azure Stack，必须从头开始重新部署，如下所述。 步骤会有所不同，具体取决于是否使用 Azure Stack 安装程序（asdk-installer.ps1）脚本来安装 ASDK。

### <a name="redeploy-the-asdk-using-the-installer-script"></a>使用安装程序脚本重新部署 ASDK
1. 在 ASDK 计算机上，打开提升的 PowerShell 控制台，并导航到位于非系统驱动器上的**AzureStack_Installer**目录中的 asdk-installer.ps1 脚本。 运行该脚本，然后单击 "**重新启动**"。

   ![运行 asdk-installer.ps1 脚本](media/asdk-redeploy/1.png)

2. 选择基本操作系统（不**Azure Stack**），然后单击 "**下一步**"。

   ![重新启动到主机操作系统](media/asdk-redeploy/2.png)

3. ASDK 主机重启到基本操作系统中后，以本地管理员身份登录。查找并删除以前的部署中使用的**C:\CloudBuilder.vhdx**文件。

4. 重复执行第一次[部署 ASDK](asdk-install.md)所需的步骤。

### <a name="redeploy-the-asdk-without-using-the-installer"></a>在不使用安装程序的情况下重新部署 ASDK
如果未使用 asdk-installer.ps1 脚本来安装 ASDK，则在重新部署 ASDK 之前，必须手动重新配置 ASDK 主计算机。

1. 通过在 ASDK 计算机上运行**msconfig.exe**来启动系统配置实用程序。 在 "**启动**" 选项卡上，选择 "主机操作系统（不 Azure Stack）"，单击 "**设置为默认值**"，然后单击 **"确定"** 。 出现提示时单击 "**重新启动**"。

      ![设置启动配置](media/asdk-redeploy/4.png)

2. 在 ASDK 主机重启到基本操作系统中后，以本地管理员身份登录到 ASDK 主机计算机。 查找并删除以前的部署中使用的**C:\CloudBuilder.vhdx**文件。

3. 重复[使用 PowerShell 首次部署 ASDK](asdk-deploy-powershell.md)所需的步骤。


## <a name="next-steps"></a>后续步骤
[Post ASDK 部署任务](asdk-post-deploy.md)




