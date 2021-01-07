---
title: Azure Stack Hub 上的应用服务 Update 1 发行说明
description: 了解 Azure Stack Hub 上的应用服务 Update 1 中的改进、修复和已知问题。
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 433bfa9f6aee13131b80f3200c092bc0d72bd65e
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974381"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>Azure Stack Hub 上的应用服务 Update 1 发行说明

这些发行说明介绍 Azure Stack Hub 上的 Azure 应用服务 Update 1 中的改进、修复和已知问题。 已知问题分为三个部分：与部署直接相关的问题、更新过程问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 请将 1802 更新应用于 Azure Stack Hub 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)，然后部署 Azure 应用服务。

## <a name="build-reference"></a>内部版本参考

Azure Stack Hub 上的应用服务 Update 1 的内部版本号为 **69.0.13698.9**。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 基于 Azure Stack Hub 的 Azure 应用服务的新部署现在要求提供三使用者通配型证书，因为在 Azure 应用服务中处理适用于 Kudu 的 SSO 的方式已改进。 新使用者为“\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>”

在开始部署之前，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

## <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack Hub 上的 Azure 应用服务 Update 1 包含以下改进和修复：

- **Azure 应用服务的高可用性** - Azure Stack Hub 1802 更新允许跨容错域部署工作负荷，这样，当你跨容错域部署应用服务基础结构时，该基础结构就是容错的。 默认情况下，Azure应用服务的所有新部署都有此功能。 但是，如果是在应用 Azure Stack Hub 1802 更新之前完成的部署，请参阅[应用服务容错域文档](azure-stack-app-service-before-you-get-started.md)。

- **在现有的虚拟网络中进行部署** - 客户现在可以在现有的虚拟网络中部署基于 Azure Stack Hub 的应用服务。 在现有的虚拟网络中进行部署以后，客户就可以通过专用端口连接到 Azure 应用服务所需的 SQL Server 和文件服务器。 在部署过程中，客户可以选择在现有的虚拟网络中进行部署，但在部署之前[必须创建供应用服务使用的子网](azure-stack-app-service-before-you-get-started.md#virtual-network)。

- 针对 **应用服务租户、管理员、函数门户和 Kudu 工具** 的更新。 与 Azure Stack Hub 门户 SDK 版本一致。

- 将 **Azure Functions 运行时** 更新到 **v1.0.11388**。

- **针对以下应用程序框架和工具的更新**：
    - 增加了 **.NET Core 2.0** 支持。
    - 增加了 **Node.JS** 版本：
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - 增加了 **NPM** 版本：
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 增加了 **PHP** 更新：
        - 5.6.32
        - 7.0.26（x86 和 x64）
        - 7.1.12（x86 和 x64）
    - 已将 **适用于 Windows 的 Git** 更新到 v2.14.1
    - 已将 **Mercurial** 更新到 v4.5.0

  - 增加了对“仅限 HTTPS”功能的支持，该功能位于应用服务用户门户的“自定义域”功能中。

  - 增加了在 Azure Functions 的自定义存储选取器中对存储连接进行验证的功能。

### <a name="fixes"></a>修复项

- 创建脱机部署包时，客户再也不会一打开应用服务安装程序中的文件夹就收到拒绝访问错误消息。

- 解决了在应用服务用户门户中使用自定义域功能时遇到的问题。

- 防止客户使用在安装过程中保留的管理员名称。

- 允许通过 **已加入域** 的文件服务器进行应用服务部署。

- 改进了在脚本中检索 Azure Stack Hub 根证书的功能，并添加了在应用服务安装程序中验证该根证书的功能。

- 修复了在删除 Microsoft.Web 命名空间中包含资源的订阅时，返回到 Azure 资源管理器的状态不正确的问题。

## <a name="known-issues-with-the-deployment-process"></a>部署过程的已知问题

- 证书验证错误。

    如果在集成的系统中进行部署，则某些客户会在向应用服务安装程序提供证书时遇到问题，因为安装程序中的验证限制性太强。 应用服务安装程序已重新发布，客户应[下载更新的安装程序](https://aka.ms/appsvconmasinstaller)。 如果在使用更新的安装程序验证证书时仍然遇到问题，请与支持部门联系。

- 从集成的系统中检索 Azure Stack Hub 根证书时遇到问题。

    Get-AzureStackRootCert.ps1 出错，导致客户在尚未安装 Azure Stack Hub 根证书的计算机上执行此脚本时无法检索该根证书。 现在，此脚本也已重新发布，解决了此问题。 [在此处下载更新的帮助程序脚本](https://aka.ms/appsvconmashelpers)。 如果在使用更新的脚本检索根证书时仍然遇到问题，请与支持部门联系。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 基于 Azure Stack Hub 的 Azure 应用服务 Update 1 的更新没有已知问题。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

- 槽交换不正常。

此版本中的站点槽交换功能无法使用。 若要还原功能，请完成以下步骤：

1. 将 ControllersNSG 网络安全组的设置修改为 **Allow**，允许通过远程桌面连接到应用服务控制器实例。 将 AppService.local 替换为部署应用服务时所在资源组的名称。

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. 浏览到 Azure Stack Hub 管理员门户中“虚拟机”下的 **CN0-VM**，单击“连接”，以便通过控制器实例打开远程桌面会话。 使用在部署应用服务期间指定的凭据。
3. 以管理员身份启动 **PowerShell** 并执行以下脚本：

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. 关闭远程桌面会话。
5. 将 ControllersNSG 网络安全组的设置还原为 **Deny**，拒绝通过远程桌面连接到应用服务控制器实例。 将 AppService.local 替换为部署应用服务时所在资源组的名称。

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. 当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理员门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：

- 源：任意
- 源端口范围：*
- 目标：IP 地址
- 目标 IP 地址范围：文件服务器的 IP 范围
- 目标端口范围：445
- 协议：TCP
- 操作：允许
- 优先级：700
- 姓名：Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>云管理员在操作基于 Azure Stack Hub 的 Azure 应用服务时的已知问题

请参阅 [Azure Stack Hub 1802 发行说明](./release-notes.md?view=azs-1802&preserve-view=true)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack Hub 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack Hub 的应用服务的部署准备，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
