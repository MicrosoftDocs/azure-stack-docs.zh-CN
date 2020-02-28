---
title: Azure Stack 集线器上的应用服务 Update 1 发行说明
description: 在 Azure Stack 集线器上的应用服务的 Update 1 中了解改进、修复和已知问题。
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 44baf18b4e7b1acb39125a4ccdeb4228557de4d4
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703513"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>Azure Stack 集线器上的应用服务 Update 1 发行说明

这些发行说明介绍 Azure Stack 中心更新 1 Azure App Service 的改进、修复和已知问题。 已知问题分为三部分：直接与部署相关的问题、更新过程的问题以及生成（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 之前，将1802更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 中心更新1生成号上的应用服务是**69.0.13698.9**。

### <a name="prerequisites"></a>必备条件

> [!IMPORTANT]
> Azure Stack 中心的 Azure App Service 的新部署现在需要[三个使用者的通配符证书](azure-stack-app-service-before-you-get-started.md#get-certificates)，因为在 Azure App Service 中处理 Kudu SSO 的方式。 新主题是 **\*\<区域\>\<domainname\>\<扩展\>**

开始部署之前，请参阅在[Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack 中心更新1上的 Azure App Service 包括以下改进和修补程序：

- **Azure App Service 的高可用性**-已启用 Azure Stack 中心1802更新的工作负荷跨容错域进行部署，使应用服务基础结构在容错域中部署时具有容错能力。 默认情况下，Azure App Service 的所有新部署都具有此功能。 但是，对于在应用 Azure Stack 中心1802更新之前完成的部署，请参阅[应用服务容错域文档](azure-stack-app-service-before-you-get-started.md)。

- **在现有虚拟网络中部署**-客户现在可以在现有虚拟网络中的 Azure Stack 集线器上部署应用服务。 在现有虚拟网络中部署可使客户能够连接到 Azure App Service 通过专用端口所需的 SQL Server 和文件服务器。 在部署过程中，客户可以选择在现有虚拟网络中部署，但必须在部署前[创建应用服务使用的子网](azure-stack-app-service-before-you-get-started.md#virtual-network)。

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 中心门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.11388**。

- **以下应用程序框架和工具的更新**：
    - 添加了 **.Net Core 2.0**支持。
    - 已添加**node.js**版本：
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
    - 添加了**NPM**版本：
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 添加了**PHP**更新：
        - 5.6.32
        - 7.0.26 （x86 和 x64）
        - 7.1.12 （x86 和 x64）
    - 已更新适用于 Windows to v v2.14.1**的 Git**
    - 将**Mercurial**更新到 v 4.5。0

  - 添加了对应用服务用户门户中 "自定义域" 功能中的 "**仅 HTTPS** " 功能的支持。

  - 在 Azure Functions 的自定义存储选取器中添加了对存储连接的验证。

#### <a name="fixes"></a>修复项

- 创建脱机部署包时，客户在从应用服务安装程序打开该文件夹时将不再收到 "拒绝访问" 错误消息。

- 在应用服务用户门户中使用 "自定义域" 功能时解决了问题。

- 防止客户在安装过程中使用保留的管理员名称。

- 已通过已**加入域**的文件服务器启用应用服务部署。

- 改进了在脚本中检索 Azure Stack 集线器根证书，并在应用服务安装程序中添加了验证根证书的功能。

- 修复了在删除包含 Microsoft Web 命名空间中的资源的订阅后，返回到 Azure 资源管理器的错误状态。

### <a name="known-issues-with-the-deployment-process"></a>部署过程中的已知问题

- 证书验证错误。

    某些客户在将证书提供给应用服务安装程序时遇到问题，因为安装程序中的验证过于严格。 应用服务安装程序已重新发布，客户应[下载更新的安装程序](https://aka.ms/appsvconmasinstaller)。 如果在通过已更新的安装程序验证证书时仍然遇到问题，请联系支持人员。

- 从集成系统检索 Azure Stack 集线器根证书时出现问题。

    当在未安装根证书的计算机上执行脚本时，Get-azurestackrootcert.ps1 中的错误导致客户无法检索 Azure Stack 中心根证书。 现在，此脚本可用于解决问题。 [在此处下载更新的帮助程序脚本](https://aka.ms/appsvconmashelpers)。 如果在通过已更新的脚本检索根证书时仍然遇到问题，请联系支持人员。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- Azure Stack 中心更新1上 Azure App Service 的更新没有已知问题。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 槽交换不起作用。

在此版本中，站点槽交换中断。 若要还原功能，请完成以下步骤：

1. 修改 ControllersNSG 网络安全组，以**允许**与应用服务控制器实例建立远程桌面连接。 将 AppService 替换为在其中部署了应用服务的资源组的名称。

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

2. 在 Azure Stack 中心管理员门户中的 "虚拟机" 下浏览到**CN0-VM** ，并**单击 "连接**" 以打开与控制器实例的远程桌面会话。 使用在应用服务部署过程中指定的凭据。
3. **以管理员身份启动 PowerShell**并执行以下脚本：

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
5. 恢复 ControllersNSG 网络安全组，以**拒绝**到应用服务控制器实例的远程桌面连接。 将 AppService 替换为在其中部署了应用服务的资源组的名称。

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

6. 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加一个出站安全规则，该规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：

- 源：任意
- 源端口范围： *
- 目标： IP 地址
- 目标 IP 地址范围：文件服务器的 Ip 范围
- 目标端口范围：445
- 协议：TCP
- 操作：允许
- 优先级：700
- 名称： Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 中心1802发行说明](azure-stack-update-1903.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述](azure-stack-app-service-overview.md)中的 Azure App Service。
- 有关如何准备在 Azure Stack 集线器上部署应用服务的详细信息，请参阅[在 Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
