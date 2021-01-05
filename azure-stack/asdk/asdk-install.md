---
title: 安装 ASDK
description: 了解如何安装 Azure Stack 开发工具包 (ASDK)。
author: PatAltimore
ms.topic: article
ms.date: 05/06/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: fe485a3eebee3e44e19173fdf68440057de7d783
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873412"
---
# <a name="install-the-asdk"></a>安装 ASDK
[准备 Azure Stack 开发工具包 (ASDK) 主机](asdk-prepare-host.md)后，可以使用本文中的以下步骤将 ASDK 部署到 CloudBuilder.vhdx 映像中。

## <a name="install-the-asdk"></a>安装 ASDK
本文中的步骤说明如何使用图形用户界面 (GUI)（可通过下载并运行 **asdk-installer.ps1** PowerShell 脚本来获取）部署 ASDK。

> [!NOTE]
> ASDK 的安装程序用户界面是一种基于 WCF 和 PowerShell 的开源脚本。


1. 在主计算机成功启动到 CloudBuilder.vhdx 映像之后，使用[准备用于 ASDK 安装的 ASDK 主计算机](asdk-prepare-host.md)时指定的管理员凭据登录。 此凭据应与 ASDK 主机本地管理员凭据相同。
2. 打开权限提升的 PowerShell 控制台并运行 **&lt;驱动器号>\AzureStack_Installer\asdk-installer.ps1** PowerShell 脚本。 请注意，该脚本现在可能位于与 CloudBuilder.vhdx 映像中的 C:\ 不同的驱动器上。 单击“安装”  。

    ![安装 ASDK](media/asdk-install/1.PNG) 

3. 在 "标识提供者 **类型** " 下拉框中，选择 " **azure 中国云**"、" **azure 美国政府云**"、" **AD FS**" 或 " **azure 云**"。 在“本地管理员密码”下的“密码”框中，键入本地管理员密码（必须与当前配置的本地管理员密码相符），然后单击“下一步”。   

    ![ASDK 中的标识提供者类型下拉列表](media/asdk-install/2.PNG) 
  
    如果选择 Azure 订阅标识提供者，则需要建立 Internet 连接、获取 *domainname*.onmicrosoft.com 格式的 Azure AD 目录租户的完整名称，或 Azure AD 验证的自定义域名。 此外，需要获取指定目录的全局管理员凭据。

    部署后，不需要 Azure Active Directory (Azure AD) 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 例如，资源提供程序安装程序脚本或需要授予权限的新功能。 可以临时复原帐户的全局管理员权限，也可以使用单独的全局管理员帐户（该帐户应是 *默认提供程序订阅* 的所有者）。

    使用 AD FS 作为标识提供者时，将使用默认标记目录服务。 登录时使用的默认帐户是 azurestackadmin@azurestack.local，要使用的密码是在设置过程中提供的。

   > [!NOTE]
   > 为获得最佳结果，即使你要使用离线 Azure Stack 环境并使用 AD FS 作为标识提供者，也最好是在连接到 Internet 的情况下安装 ASDK。 这样就可以在部署时激活 ASDK 安装版随附的 Windows Server 2016 评估版。

4. 选择用于 ASDK 的网络适配器，然后单击“下一步”。 

    ![选择 ASDK 的网络适配器](media/asdk-install/3.PNG)

5. 在“网络配置”  页上，提供有效的 **时间服务器 IP** 地址。 此必填字段设置可供 ASDK 使用的时间服务器。 必须以有效的时间服务器 IP 地址的形式提供此参数。 不支持服务器名称。

      > [!TIP]
      > 若要查找时间服务器 IP 地址，请访问 [ntppool.org](https://www.ntppool.org/) 或 ping time.windows.com。 

    **（可选）** 可以提供 **DNS 转发器** IP 地址。 在 Azure Stack 部署过程中会创建 DNS 服务器。 若要允许解决方案中的计算机解析标记外部的名称，请提供现有的基础结构 DNS 服务器。 标记内 DNS 服务器将未知的名称解析请求转发至此服务器。

    ![ASDK 中的 DNS 转发器和网络配置](media/asdk-install/4.PNG)

6. 在“验证网络接口卡属性”页上会看到一个进度栏。  完成验证后，单击“下一步”  。

    ![验证 ASDK 中的网络接口卡属性](media/asdk-install/5.PNG)

7. 在“摘要”页上单击“部署”，开始在 ASDK 主机上安装 ASDK。  

    ![在 ASDK 中部署之前的脚本摘要](media/asdk-install/6.PNG)

    > [!TIP]
    > 也可在这里复制将要用来安装 ASDK 的 PowerShell 安装程序命令。 如果需要[使用 PowerShell 在主机上重新部署 ASDK](asdk-deploy-powershell.md)，此操作会有所帮助。

8. 如果执行 Azure AD 部署，系统会在安装开始后数分钟提示输入 Azure AD 全局管理员帐户凭据。

9. 部署程序需要花费数小时，在此期间，主机会自动重新启动一次。 若要监视部署进度，请在 ASDK 主机重启后，以 azurestack\AzureStackAdmin 身份登录。 如果部署成功，PowerShell 控制台会显示“COMPLETE:  Action 'Deployment'”。 
    > [!IMPORTANT]
    > 如果在计算机加入 azurestack 域后以本地管理员身份登录，则看不到部署进度。 请勿重新运行部署，而应以 azurestack\AzureStackAdmin 身份登录，验证其是否正在运行。

    ![ASDK 部署成功](media/asdk-install/7.PNG)

祝贺你，现已成功安装 ASDK！

如果部署出于某种原因失败，可以从头[重新部署](asdk-redeploy.md)，也可以使用以下 PowerShell 命令，从最后一个成功步骤重新开始部署。 可在同一个权限提升的 PowerShell 窗口中使用这些命令。

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>后续步骤
[部署后的配置](asdk-post-deploy.md)
