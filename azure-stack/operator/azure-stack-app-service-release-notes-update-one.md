---
title: Azure Stack Update 1 发行说明中的应用服务 |Microsoft Docs
description: 了解 Azure Stack 上应用服务的更新1中的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 7be74701b3e25658258abc7102668346e584ab39
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974830"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Azure Stack 上的应用服务 Update 1 发行说明

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍 Azure Stack Update 1 Azure App Service 上的改进、修复和已知问题。 已知问题分为三部分：直接与部署相关的问题、更新过程的问题以及生成（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 之前，将1802更新应用到 Azure Stack 集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 上的应用服务 Update 1 内部版本号为**69.0.13698.9**。

### <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> Azure Stack 上的 Azure App Service 的新部署现在需要[三个使用者通配符证书](azure-stack-app-service-before-you-get-started.md#get-certificates)，因为在 Azure App Service 中处理 KUDU 的 SSO。 新的使用者是 **\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>**

开始部署之前，请参阅[Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

### <a name="new-features-and-fixes"></a>新功能和修复

基于 Azure Stack 的 Azure 应用服务 Update 1 包含以下改进和修复：

- **Azure App Service 的高可用性**-已启用 Azure Stack 1802 更新的工作负荷跨容错域进行部署，使应用服务基础结构在容错域中部署时具有容错能力。 默认情况下，Azure App Service 的所有新部署都具有此功能。 但是，对于在应用 Azure Stack 1802 更新之前完成的部署，请参阅[应用服务容错域文档](azure-stack-app-service-before-you-get-started.md)。

- **在现有的虚拟网络中进行部署** - 客户现在可以在现有的虚拟网络中部署基于 Azure Stack 的应用服务。 在现有虚拟网络中部署可使客户能够连接到 Azure App Service 通过专用端口所需的 SQL Server 和文件服务器。 在部署过程中，客户可以选择在现有虚拟网络中部署，但必须在部署前[创建应用服务使用的子网](azure-stack-app-service-before-you-get-started.md#virtual-network)。

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将 **Azure Functions 运行时**更新到 **v1.0.11388**。

- **针对以下应用程序框架和工具的更新**：
    - 添加了 **.Net Core 2.0**支持。
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
    - 添加了**PHP**更新：
        - 5.6.32
        - 7.0.26（x86 和 x64）
        - 7.1.12（x86 和 x64）
    - 已将**适用于 Windows 的 Git** 更新到 v2.14.1
    - 已将 **Mercurial** 更新到 v4.5.0

  - 增加了对“仅限 HTTPS”功能的支持，该功能位于应用服务租户门户的“自定义域”功能中。

  - 在 Azure Functions 的自定义存储选取器中添加了对存储连接的验证。

#### <a name="fixes"></a>修复项

- 创建脱机部署包时，客户在从应用服务安装程序打开该文件夹时将不再收到 "拒绝访问" 错误消息。

- 使用应用服务租户门户中的 "自定义域" 功能时解决了问题。

- 防止客户在安装过程中使用保留的管理员名称。

- 已通过已**加入域**的文件服务器启用应用服务部署。

- 改进了在脚本中检索 Azure Stack 根证书，并添加了在应用服务安装程序中验证根证书的功能。

- 修复了在删除 Microsoft.Web 命名空间中包含资源的订阅时，返回到 Azure 资源管理器的状态不正确的问题。

### <a name="known-issues-with-the-deployment-process"></a>部署过程的已知问题

- 证书验证错误。

    某些客户在将证书提供给应用服务安装程序时遇到问题，因为安装程序中的验证过于严格。 应用服务安装程序已重新发布，客户应[下载更新的安装程序](https://aka.ms/appsvconmasinstaller)。 如果在使用更新的安装程序验证证书时仍然遇到问题，请与支持部门联系。

- 从集成的系统中检索 Azure Stack 根证书时遇到问题。

    当在未安装根证书的计算机上执行脚本时，Get-azurestackrootcert.ps1 中的错误导致客户无法检索 Azure Stack 根证书。 现在，此脚本可用于解决问题。 [在此处下载更新的帮助程序脚本](https://aka.ms/appsvconmashelpers)。 如果在使用更新的脚本检索根证书时仍然遇到问题，请与支持部门联系。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 基于 Azure Stack 的 Azure 应用服务 Update 1 的更新没有已知问题。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 槽交换不起作用。

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

2. 在 Azure Stack 管理员门户中的 "虚拟机" 下浏览到**CN0-VM** ，并**单击 "连接**" 以打开与控制器实例的远程桌面会话。 使用在部署应用服务期间指定的凭据。
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

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加一个出站安全规则，该规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：

- 源：任意
- 源端口范围：*
- 目标：IP 地址
- 目标 IP 地址范围：文件服务器的 IP 范围
- 目标端口范围：445
- 协议：TCP
- 操作：Allow
- 优先级：700
- 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure Stack 上的云管理员操作 Azure App Service 的已知问题

请参阅 [Azure Stack 1802 发行说明](azure-stack-update-1903.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[在 Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
