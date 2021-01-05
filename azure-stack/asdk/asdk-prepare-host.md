---
title: 准备 ASDK 主机
description: 了解如何准备用于 ASDK 安装的 Azure Stack 开发工具包 (ASDK) 主计算机。
author: PatAltimore
ms.topic: article
ms.date: 08/28/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: 5745d7efc5a953abaaa4e1d878677131c1c6e709
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873327"
---
# <a name="prepare-the-asdk-host-computer"></a>准备 ASDK 主机
在主计算机上安装 Azure Stack 开发工具包 (ASDK) 之前，必须先准备好用于安装的 ASDK 主机。 准备好主机后，它将从 CloudBuilder.vhdx 虚拟机 (VM) 硬盘驱动器启动，以开始 ASDK 部署。

## <a name="prepare-the-development-kit-host-computer"></a>准备开发工具包主机
在主机上安装 ASDK 之前，必须先准备 ASDK 主机环境。 若要准备该环境，请执行以下步骤：

1. 以本地管理员身份登录到 ASDK 主计算机。
2. 确保已将 CloudBuilder.vhdx 文件移到 C:\ 驱动器的根目录 (`C:\CloudBuilder.vhdx`)。
3. 运行以下脚本，将 ASDK 安装程序文件 (asdk-installer.ps1) 从 [Azure Stack GitHub 工具存储库](https://github.com/Azure/AzureStack-Tools)下载到 ASDK 主计算机上的 **C:\AzureStack_Installer** 文件夹：

   > [!IMPORTANT]
   > 每次安装 ASDK 时，请务必下载 asdk-installer.ps1 文件。 此脚本经常发生更改，应该为每个 ASDK 部署使用最新版本。 旧版本的脚本可能无法与最新版本一起使用。

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. 从提升了权限的 PowerShell 控制台启动 **C:\AzureStack_Installer\asdk-installer.ps1** 脚本，然后单击“准备环境”。 

    ![在 ASDK 中准备环境](media/asdk-prepare-host/1.PNG) 

5. 在安装程序的“选择 Cloudbuilder vhdx”页上浏览到 **cloudbuilder.vhdx** 文件并将其选中，该文件是在 [前述步骤](asdk-download.md)中下载并提取的。  在此页上，如果需要向 ASDK 工具包主计算机添加其他驱动程序，还可以启用“添加驱动程序”  复选框。 单击“下一步”  。  

    ![选择 cloudbuilder.vhdx 并将驱动程序添加到 ASDK](media/asdk-prepare-host/2.PNG)

6. 在“可选设置”  页上，提供 ASDK 主计算机的本地管理员帐户信息，然后单击“下一步”  。

    如果在此步骤中未提供本地管理员凭据，则在设置 ASDK 过程中重启计算机后，需要直接访问或通过 KVM 访问主机。

   ![ASDK 中的可选设置-提供本地管理员帐户信息](media/asdk-prepare-host/3.PNG)

    还可以提供以下可选设置的值：
    - **计算机名**：此选项设置 ASDK 主机的名称。 名称必须符合 FQDN 要求，且长度不得超过 15 个字符。 默认值是由 Windows 生成的随机计算机名称。

        - 选择网络适配器。 确保可以连接到该适配器，然后单击“下一步”  。

            ![网络适配器设置的屏幕截图](media/asdk-prepare-host/step-four-network-adapter.png)

        - 请确保显示的 **IP 地址**、**网关** 和 **DNS** 值正确，提供有效的 **时间服务器 IP** 地址，然后单击“下一步”  。

            >[!TIP]
            >若要查找时间服务器 IP 地址，请访问 [ntppool.org](https://www.ntppool.org/) 或 ping time.windows.com。 

            ![IP 配置设置的屏幕截图](media/asdk-prepare-host/step-five-host-ip-config.png)

7. 单击“下一步”  ，启动准备过程。
8. 但准备过程指示“已完成”时，  单击“下一步”  。

    ![在 ASDK 中准备环境](media/asdk-prepare-host/4.PNG)

9. 单击“立即重新启动”，  将 ASDK 主计算机启动到 cloudbuilder.vhdx 中，然后[继续部署过程](asdk-install.md)。

    ![重新启动 ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>后续步骤
[安装 ASDK](asdk-install.md)
