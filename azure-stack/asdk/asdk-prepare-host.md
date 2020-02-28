---
title: 准备 ASDK 主机计算机
description: 了解如何准备用于 ASDK 安装的 Azure Stack 开发工具包（ASDK）主机计算机。
author: justinha
ms.topic: article
ms.date: 08/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: ba64a3bfbef9601d9cc8d8a28176a84a9403326a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696016"
---
# <a name="prepare-the-asdk-host-computer"></a>准备 ASDK 主机计算机
必须先准备好安装 ASDK 主机，然后才能在主计算机上安装 Azure Stack 开发工具包（ASDK）。 准备好主机后，它将从 Cloudbuilder.vhdx 虚拟机（VM）硬盘启动，开始 ASDK 部署。

## <a name="prepare-the-development-kit-host-computer"></a>准备开发工具包主机
在主计算机上安装 ASDK 之前，必须准备好 ASDK 主机计算机环境。 若要准备该环境，请执行以下步骤：

1. 以本地管理员身份登录到你的 ASDK 主计算机。
2. 确保已将 Cloudbuilder.vhdx 文件移动到 C：\ 的根目录驱动器（`C:\CloudBuilder.vhdx`）。
3. 运行以下脚本，将 ASDK 安装程序文件（asdk-installer.ps1）从[Azure Stack GitHub 工具存储库](https://github.com/Azure/AzureStack-Tools)下载到 ASDK 主机计算机上的**c：\ AzureStack_Installer**文件夹：

   > [!IMPORTANT]
   > 请确保每次安装 ASDK 时下载 asdk-installer.ps1 文件。 对此脚本进行频繁更改，最新版本应用于每个 ASDK 部署。 旧版本的脚本可能不适用于最新版本。

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

4. 在已提升权限的 PowerShell 控制台中，启动**c：\ AzureStack_Installer \asdk-installer.ps1**脚本，然后单击 "**准备环境**"。

    ![在 ASDK 中准备环境](media/asdk-prepare-host/1.PNG) 

5. 在安装程序的 "**选择 cloudbuilder.vhdx vhdx** " 页上，浏览到并选择你在[前面的步骤](asdk-download.md)中下载并提取的**cloudbuilder.vhdx**文件。 如果需要将其他驱动程序添加到 ASDK 工具包主机，则还可以在此页上启用 "**添加驱动程序**" 复选框。 单击“下一步”。  

    ![选择 cloudbuilder.vhdx 并将驱动程序添加到 ASDK](media/asdk-prepare-host/2.PNG)

6. 在 "**可选设置**" 页上，提供 ASDK 主计算机的本地管理员帐户信息，然后单击 "**下一步**"。

    如果在此步骤中未提供本地管理员凭据，则在设置 ASDK 的过程中，在计算机重新启动后，你将需要直接或通过 KVM 访问主机。

   ![ASDK 中的可选设置-提供本地管理员帐户信息](media/asdk-prepare-host/3.PNG)

    你还可以为以下可选设置提供值：
    - **Computername**：此选项设置 ASDK 主机的名称。 该名称必须符合 FQDN 要求，且长度必须小于或等于15个字符。 默认值为 Windows 生成的随机计算机名称。

        - 选择网络适配器。 在单击 "**下一步**" 之前，请确保可以连接到适配器。

            ![网络适配器设置的屏幕截图](media/asdk-prepare-host/step-four-network-adapter.png)

        - 请确保显示的**IP 地址**、**网关**和**DNS**值正确，提供有效的**时间服务器 IP**地址，然后单击 "**下一步**"。

            >[!TIP]
            >若要查找时间服务器 IP 地址，请访问[ntppool.org](https://www.ntppool.org/)或 ping time.windows.com。 

            ![IP 配置设置的屏幕截图](media/asdk-prepare-host/step-five-host-ip-config.png)

7. 单击 "**下一步**" 开始准备过程。
8. 准备工作**完成**后，单击 "**下一步**"。

    ![在 ASDK 中准备环境](media/asdk-prepare-host/4.PNG)

9. 单击 "**立即重新启动**"，将 ASDK 主机启动到 cloudbuilder.vhdx 中，然后[继续部署过程](asdk-install.md)。

    ![重新启动 ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>后续步骤
[安装 ASDK](asdk-install.md)
