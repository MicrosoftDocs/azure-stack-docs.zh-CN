---
title: Microsoft Azure Stack 开发工具包发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 05/01/2019
ms.openlocfilehash: 935f144ebbb40da66ac43fc8e9d5dfc7c3e3d0b6
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64983599"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文介绍了 Azure Stack 开发工具包 (ASDK) 中的更改、修复和已知问题。 如果不确定所运行的版本，可以[使用门户检查版本](../operator/azure-stack-updates.md#determine-the-current-version)。

及时了解什么是 ASDK 中的新增功能通过订阅[ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS 源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)。

## <a name="build-11904036"></a>生成 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1904.md#whats-in-this-update)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 修复了识别的 VPN 连接问题[在这里，版本中的 1902年](#known-issues)。

- 此版本中修复其他 Azure Stack 问题的列表，请参阅[本节](../operator/azure-stack-release-notes-1904.md#fixes)Azure Stack 的发行说明。
- 有关已知问题的列表，请参阅[这篇文章](../operator/azure-stack-release-notes-known-issues-1904.md)。
- 请注意，[发布的 Azure Stack 修补程序](../operator/azure-stack-release-notes-1904.md#hotfixes)不适用于 Azure Stack ASDK。

## <a name="build-1903"></a>生成 1903

1903 有效负载不包括 ASDK 发行。

## <a name="build-11902069"></a>内部版本 1.1902.0.69

### <a name="new-features"></a>新增功能

- 1902 版本在 Azure Stack 管理员门户上引入了新的用户界面用于创建计划、套餐、配额和附加计划。 有关详细信息（包括屏幕截图），请参阅[创建计划、套餐和配额](../operator/azure-stack-create-plan.md)。

- 有关此版本中其他更改和改进的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-update-1902.md#improvements)。

<!-- ### New features

- For a list of new features in this release, see [this section](../operator/azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../operator/azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../operator/azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../operator/azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>已知问题

- 建立从另一台主机到中的步骤将 ASDK 的 VPN 连接出现问题[这篇文章](asdk-connect.md)。 当你尝试从 VPN 客户端连接到 ASDK 环境时，您将看到一个错误，**用户名或密码不正确**，即使您确定使用了正确的帐户，但输入的密码正确。 问题在于不能与你的凭据，而与使用 ASDK 上的 VPN 连接的身份验证协议的更改。 若要解决此问题，请执行以下步骤：

   首先，对使用 ASDK 服务器端的身份验证协议进行更改：

   1. 通过 rdp 连接到 ASDK 主机。
   2. 打开提升的 PowerShell 会话，以 azurestack\azurestackadmin 身份，使用在部署时提供的密码登录。
   3. 运行以下命令：

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   接下来，修改客户端连接脚本。 若要执行此操作的最简单方法是直接对 C:\AzureStack-Tools-master\connect\azurestack.connect.psm1 脚本模块进行更改：

   1. 修改**添加 AzsVpnConnection** cmdlet 来更改`AuthenticationMethod`参数从`MsChapv2`到`EAP`:

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. 更改**Connect AzsVpn**中使用的 cmdlet`rasdial @ConnectionName $User $PlainPassword`使用`rasphone`，如 EAP 需要交互式登录：

      ```powershell
      rasphone $ConnectionName
      ```

   3. 保存所做的更改，然后重新导入**azurestack.connect.psm1**模块。
   4. 请按[此文](asdk-connect.md#set-up-vpn-connectivity)说明执行操作。
   5. 当您连接到通过 VPN ASDK 时，连接通过导航到 Windows**网络和 Internet 设置**，然后**VPN**，而不是连接在任务栏中，以确保系统会提示输入凭据。

- 我们已识别到以下问题：发往内部负载均衡器 (ILB) 的超过 1450 字节的数据包将被丢弃。 该问题的原因是主机上的 MTU 设置过小，无法容纳遍历角色的 VXLAN 封装数据包，自版本 1901 开始，该角色已移到主机。 在你可能遇到的至少两种情况中，我们已发现此问题会自行显现：

  - SQL 查询发往内部负载均衡器 (ILB) 后面的 SQL Always On，并且超过 660 字节。
  - 如果尝试启用多个主机，Kubernetes 部署将失败。  

  在同一虚拟网络但不同子网中的 VM 与 ILB 之间进行通信时，将发生此问题。 在 ASDK 主机上权限提升的命令提示符下运行以下命令即可解决此问题：

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>内部版本 1.1901.0.95

请参阅 [Azure Stack 发行说明中的重要版本信息](../operator/azure-stack-update-1901.md#build-reference)。

### <a name="changes"></a>更改

此内部版本包含对 Azure Stack 的以下改进：

- BGP 和 NAT 组件现在部署在物理主机上。 这样就不需要通过两个公共的或公司的 IP 地址来部署 ASDK，同时也简化了部署。
- Azure Stack 集成系统备份现在可以通过 **asdk-installer.ps1** PowerShell 脚本进行[验证](asdk-validate-backup.md)。

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-update-1901.md#new-features)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 如需此版本中已修复问题的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-update-1901.md#fixed-issues)。 如需已知问题的列表，请参阅[此部分](../operator/azure-stack-update-1901.md#known-issues-post-installation)。
- 请注意，[发布的 Azure Stack 修补程序](../operator/azure-stack-update-1901.md#azure-stack-hotfixes)不适用于 Azure Stack ASDK。
