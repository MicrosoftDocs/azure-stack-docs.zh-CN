---
title: 安装 ASDK
description: 了解如何安装 Azure Stack 开发工具包（ASDK）。
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: b85279aff5427e0f3dbdc15b979a00a41db43e57
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695846"
---
# <a name="install-the-asdk"></a>安装 ASDK
[准备 ASDK 主机计算机](asdk-prepare-host.md)后，可以使用本文中的以下步骤将 AZURE STACK 开发工具包（ASDK）部署到 cloudbuilder.vhdx 映像中。

## <a name="install-the-asdk"></a>安装 ASDK
本文中的步骤说明了如何使用通过下载并运行**Asdk-installer.ps1** PowerShell 脚本提供的图形用户界面（GUI）来部署 ASDK。

> [!NOTE]
> ASDK 的安装程序用户界面是一种基于 WCF 和 PowerShell 的开源脚本。


1. 在将主机成功启动到 Cloudbuilder.vhdx 映像后，使用在为 ASDK 安装[准备 ASDK 主机](asdk-prepare-host.md)时指定的管理员凭据进行登录。 这应该与 ASDK 宿主本地管理员凭据相同。
2. 打开提升了权限的 PowerShell 控制台，并运行 **&lt;驱动器号 > \ AzureStack_Installer \Asdk-installer.ps1** PowerShell 脚本。 请注意，该脚本现在可能位于与 C：\ 不同的驱动器上在 Cloudbuilder.vhdx 映像中。 单击“安装”。

    ![安装 ASDK](media/asdk-install/1.PNG) 

3. 在 "标识提供者**类型**" 下拉框中，选择 " **azure 中国云**"、" **azure 美国政府云**"、" **AD FS**" 或 " **azure 云**"。 在 "**本地管理员密码**" 下的 "**密码**" 框中键入本地管理员密码（必须与当前配置的本地管理员密码相匹配），然后单击 "**下一步**"。

    ![ASDK 中的 "标识提供者类型" 下拉](media/asdk-install/2.PNG) 
  
    如果选择 Azure 订阅标识提供者，则需要 internet 连接、Azure AD 目录租户的完整名称，*格式为 onmicrosoft.com*或 Azure AD 验证自定义域名。 还需要指定目录的全局管理员凭据。

    部署后，不需要 Azure Active Directory （Azure AD）全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 例如，需要授予权限的资源提供程序安装程序脚本或新功能。 可以暂时恢复帐户的全局管理员权限，也可以使用*默认提供程序订阅*所有者的单独全局管理员帐户。

    当使用 AD FS 作为标识提供程序时，将使用默认的戳记目录服务。 用于登录的默认帐户是 azurestackadmin@azurestack.local的，使用的密码是在设置过程中提供的密码。

   > [!NOTE]
   > 为了获得最佳结果，即使要使用 AD FS 作为标识提供者的断开连接 Azure Stack 环境，最好在连接到 internet 的情况下安装 ASDK。 这样，可在部署时激活 ASDK 安装附带的 Windows Server 2016 评估版。

4. 选择要用于 ASDK 的网络适配器，然后单击 "**下一步**"。

    ![为 ASDK 选择网络适配器](media/asdk-install/3.PNG)

5. 在 "**网络配置**" 页上，提供有效的**时间服务器 IP**地址。 此必填字段设置 ASDK 要使用的时间服务器。 此参数必须作为有效的时间服务器 IP 地址提供。 服务器名称不受支持。

      > [!TIP]
      > 若要查找时间服务器 IP 地址，请访问[ntppool.org](https://www.ntppool.org/)或 ping time.windows.com。 

    **还**可以提供**DNS 转发器**IP 地址。 DNS 服务器作为 Azure Stack 部署的一部分进行创建。 若要允许解决方案内部的计算机解析 stamp 之外的名称，请提供现有的基础结构 DNS 服务器。 戳记 DNS 服务器将未知的名称解析请求转发到此服务器。

    ![ASDK 中的 DNS 转发器和网络配置](media/asdk-install/4.PNG)

6. 在 "**验证网络接口卡属性**" 页上，你将看到一个进度栏。 验证完成后，单击 "**下一步**"。

    ![验证 ASDK 中的网络接口卡属性](media/asdk-install/5.PNG)

7. 在 "**摘要**" 页上，单击 "**部署**" 以启动 ASDK 主机计算机上的 ASDK 安装。

    ![在 ASDK 中部署之前编写脚本摘要](media/asdk-install/6.PNG)

    > [!TIP]
    > 你还可以在此处复制用于安装 ASDK 的 PowerShell 安装命令。 如果需要[使用 PowerShell 在主机计算机上重新部署 ASDK](asdk-deploy-powershell.md)，这会很有帮助。

8. 如果执行的是 Azure AD 部署，则在安装程序启动后的几分钟内，系统将提示您输入 Azure AD 全局管理员帐户凭据。

9. 部署过程需要数小时，在这段时间内，主机将自动重启一次。 如果要监视部署进度，请在 ASDK 主机重新启动后以 azurestack\AzureStackAdmin 的身份登录。 如果部署成功，PowerShell 控制台会显示 "**完成：操作" "部署"** 。 
    > [!IMPORTANT]
    > 如果在计算机加入到 test-azurestack 域后以本地管理员身份登录，则不会看到部署进度。 不要重新运行部署，而是以 azurestack\AzureStackAdmin 的身份登录，以验证它是否正在运行。

    ![ASDK 部署成功](media/asdk-install/7.PNG)

恭喜，你已成功安装 ASDK！

如果部署由于某些原因而失败，可以从头开始[重新部署，也可以使用](asdk-redeploy.md)以下 PowerShell 命令，从最后一个成功步骤重新开始部署。 可在相同的提升 Powershell 窗口中使用命令：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>后续步骤
[部署后配置](asdk-post-deploy.md)
