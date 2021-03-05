---
title: Azure Stack 1805 更新 |Microsoft Docs
description: 了解 Azure Stack 集成系统 1805 更新的新增功能，包括已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 9f68e3f1dc3c789ec0b3b4f247037d49e43f8a42
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187089"
---
# <a name="azure-stack-1805-update"></a>Azure Stack 1805 更新

*适用于：Azure Stack 集成系统*

本文介绍 1805 更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]        
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考    
Azure Stack 1805 更新内部版本号为 **1.1805.1.47**。  

> [!TIP]  
> 根据客户的反馈，将对用于 Microsoft Azure Stack 的版本架构进行更新。  从 1805 更新开始，新架构更能代表当前的云版本。  
> 
> 版本架构目前为 *Version.YearYearMonthMonth.MinorVersion.BuildNumber*，其中的第二和第三组数字表示版本和发行版。 例如，1805.1 表示 1805 的正式发布 (RTM) 版本。  


### <a name="new-features"></a>新增功能
此更新包含对 Azure Stack 的以下改进。
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack 现已将 *Syslog* 客户端** 包含为预览功能。 此客户端允许将与 Azure Stack 基础结构相关的审核和安全日志转发到 Azure Stack 外部的 Syslog 服务器或安全信息和事件管理 (SIEM) 软件。 目前，Syslog 客户端仅支持通过默认端口 514 建立的未经身份验证的 UDP 连接。 每条 Syslog 消息的有效负载采用通用事件格式 (CEF)。 

  若要配置 Syslog 客户端，请使用特权终结点中公开的 **Set-SyslogServer** cmdlet。 

  此预览版中可能会出现以下三种警报。 当 Azure Stack 显示这些警报时，警报中会包含说明和补救指导。 
  - 标题：代码完整性关闭  
  - 标题：代码完整性处于审核模式 
  - 标题：已创建用户帐户

  此功能目前以预览版提供，在生产环境中请不要依赖此功能。   




### <a name="fixed-issues"></a>已修复的问题

<!-- # - applicability -->
- 我们修复了管理门户中阻止[从下拉列表建立新支持请求](../azure-stack-manage-portals.md#quick-access-to-help-and-support)的问题。 此选项现在按预期工作。 

- Azure Stack 所使用的性能、稳定性、安全性和操作系统的 **各种修补程序**。


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>准备阶段    

### <a name="prerequisites"></a>先决条件
- 在应用 Azure Stack 1805 更新之前安装 Azure Stack 1804 更新。  
- 安装最新可用的 1804 版更新或修补程序。   
- 开始安装更新 1805 之前，请先运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 来验证 Azure Stack 的状态，并解决出现的所有操作问题。 另外，请查看活动警报，并解决所有需要采取措施的警报。 

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题   
- 在1805更新的安装过程中，你可能会看到 *FaultType UserAccounts* 的标题为 "错误" 的警报。  可以放心地忽略这些警报。 更新到 1805 之后，这些警报将自动关闭。   

<!-- 2489559 - IS --> 
- 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。


### <a name="post-update-steps"></a>更新后步骤
安装 1805 之后，请安装所有适用的修补程序。 有关详细信息，请查看以下知识库文章，以及我们的[服务策略](../azure-stack-servicing-policy.md)。  
 - [KB 4344102 - Azure Stack 修补程序 1.1805.7.57](https://support.microsoft.com/help/4344102)。


## <a name="known-issues-post-installation"></a>已知问题（安装后）
下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户  

- Azure Stack 技术文档重点介绍了最新版本。 由于版本之间的门户更改，你在使用 Azure Stack 门户时看到的内容可能与在文档中看到的内容不同。 

<!-- 2931230 IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不能通过将 OEM 扩展包用于此版本的 Azure Stack 来应用驱动程序更新。  对于此问题，目前没有解决方法。

<!-- 2551834 - IS, ASDK --> 
- 在管理员门户或用户门户中选择存储帐户的“概述”时，“概要”窗格中的信息不会显示。  “概要”窗格显示有关帐户的信息，例如其资源组、位置和订阅 ID。  可以访问“概述”中的其他选项，例如“服务”和“监视”，以及“在资源管理器中打开”或“删除存储帐户”。 

  若要查看未显示的信息，请使用 [Get-azureRMstorageaccount](/powershell/module/azurerm.storage/get-azurermstorageaccount?preserve-view=true&view=azurermps-6.2.0) PowerShell cmdlet。 

<!-- 2551834 - IS, ASDK --> 
- 在管理员门户或用户门户中选择存储帐户的“标记”时，信息无法加载且不显示。  

  若要查看未显示的信息，请使用 [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag?preserve-view=true&view=azurermps-6.2.0) PowerShell cmdlet。


<!-- 2332636 - IS -->  
- 对 Azure Stack 标识系统使用 AD FS 并更新到此版本的 Azure Stack 时，默认提供程序订阅的默认所有者将重置为内置的 **CloudAdmin** 用户。  
  解决方法：若要在安装此更新后解决该问题，请使用[触发自动化以便在 Azure Stack 中配置声明提供程序信任](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub)过程中的步骤 3 来重置默认提供程序订阅的所有者。   

<!-- TBD - IS ASDK --> 
- 某些管理订阅类型不可用。 将 Azure Stack 升级到此版本时，控制台中不会显示版本 1804 引入的两个订阅类型。 这是正常情况。 不可用的订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。  

<!-- 2403291 - IS ASDK --> 
- 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航导航到门户中的上一个边栏选项卡，方法是在门户左上角的 "痕迹导航" 列表中选择要查看的边栏选项卡的名称。

<!-- TBD - IS --> 
- 无法在管理员门户中查看计算或存储资源。 此问题的原因是更新安装过程中出错，导致系统错误地将更新报告为成功。 如果发生此问题，请联系 Microsoft 客户支持服务部门以寻求帮助。

<!-- TBD - IS --> 
- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

<!-- TBD - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


### <a name="health-and-monitoring"></a>运行状况和监视
<!-- 1264761 - IS ASDK -->  
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  
 
   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心忽略警报 #1 和 #2，它们在一段时间后会自动关闭。 

  你可能还会看到针对 *容量* 的以下警报。 对于此警报，说明中标识的可用内存百分比可能会有所不同：  

  警报 #3：
   - 名称：内存容量低
   - 严重性：严重
   - 组件：容量
   - 说明：该区域已使用了 80.00% 以上的可用内存。 使用大量内存创建虚拟机可能会失败。  

  在此版本的 Azure Stack 中，该警报可能会错误地触发。 如果租户虚拟机继续成功部署，则可以放心地忽略此警报。 
  
  警报 #3 不会自动关闭。 如果你关闭此警报，Azure Stack 将在 15 分钟内创建相同的警报。  

<!-- 2368581 - IS. ASDK --> 
- 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能指示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。 


### <a name="compute"></a>计算
<!-- TBD - IS, ASDK --> 
- 选择虚拟机大小进行虚拟机部署时，某些 F 系列 VM 大小在创建 VM 时显示的大小选择器中不可见。 以下 VM 大小不显示在选择器中：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
  解决方法是，使用下列方法之一部署 VM。 在每种方法中，都需要指定要使用的 VM 大小。

  - **Azure 资源管理器模板：** 使用模板时，请将模板中的 *vmSize* 设置为想要使用的 VM 大小。 例如，以下条目用于部署使用 *F32s_v2* 大小的 VM：  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI：** 可以使用 [az vm create](/cli/azure/vm?preserve-view=true&view=azure-cli-latest#az-vm-create) 命令并将 VM 大小指定为参数，类似于 `--size "Standard_F32s_v2"`。

  - **PowerShell：** 通过 PowerShell，可以将 [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig?preserve-view=true&view=azurermps-6.0.0) 与指定了 VM 大小的参数一起使用，类似于 `-VMSize "Standard_F32s_v2"`。


<!-- TBD - IS ASDK --> 
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

<!-- TBD - IS --> 
-  通过转到“新建” > **“计算”** > “可用性集”在门户中创建可用性集时，只能创建 1 个包含 1 个容错域和 1 个更新域的可用性集。 解决方法是在创建新的虚拟机时，通过 PowerShell、CLI 或门户来创建可用性集。

<!-- TBD - IS ASDK --> 
- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

<!-- TBD - IS ASDK --> 
- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  


### <a name="networking"></a>网络
<!-- TBD - IS ASDK --> 
- 无法在管理员或用户门户中创建用户定义的路由。 解决方法是使用 [Azure PowerShell](/azure/virtual-network/tutorial-create-route-table-powershell)。

<!-- 1766332 - IS ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 2388980 - IS ASDK --> 
- 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原始 VM，而不是新的 VM。

<!-- 2292271 - IS ASDK --> 
- 如果提高属于某个套餐和计划的网络资源的配额限制，而该套餐和计划与租户订阅相关联，则新的限制不会应用到该订阅。 但是，新限制会应用到在配额提高后创建的新订阅。

  若要解决此问题，请在计划已与订阅关联时使用附加计划来增加网络配额。 有关详细信息，请参阅如何[提供附加计划](../azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

<!-- 2304134 IS ASDK --> 
- 不能删除与 DNS 区域资源或路由表资源相关联的订阅。 若要成功地删除该订阅，必须先从租户订阅中删除 DNS 区域资源和路由表资源。


<!-- 1902460 - IS ASDK --> 
-  Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

<!-- 16309153 - IS ASDK --> 
- 在使用 *自动* 的 DNS 服务器设置创建的虚拟网络上，更改为自定义 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 VM。

<!-- TBD - IS ASDK --> 
- Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。

<!-- 2096388 IS --> 
- 不能使用管理员门户更新网络安全组的规则。

    针对应用服务的解决方法：如需通过远程桌面连接到控制器实例，请使用 PowerShell 修改网络安全组中的安全规则。  以下示例说明了如何先将配置设置为 *allow*，然后再还原为 *deny*：  

    - *禁止*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn't work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

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

    - *注销*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn't work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

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


### <a name="sql-and-mysql"></a>SQL 和 MySQL

<!-- TBD - IS --> 
- 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。  

<!-- IS, ASDK --> 
-  为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列** 或 **层级** 名称中不支持使用特殊字符（包括空格和句点）。

<!-- TBD - IS --> 
> [!NOTE]  
> 更新到 Azure Stack 1805 后，可以继续使用以前部署的 SQL 和 MySQL 资源提供程序。  建议在新版本发布后更新 SQL 和 MySQL。 与 Azure Stack 一样，请将更新按顺序应用到 SQL 和 MySQL 资源提供程序。 例如，如果使用版本 1803，请先应用版本 1804，然后更新到 1805。      
>   
> 安装更新 1805 不会影响用户当前对 SQL 或 MySQL 资源提供程序的使用。
> 不管所用资源提供程序的版本如何，在其数据库中的用户数据不会受到影响，仍然可用。    



### <a name="app-service"></a>应用服务
<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

<!-- 2489178 - IS ASDK --> 
- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。

<!-- TBD - IS ASDK --> 
- 目前，应用服务只能部署到“默认提供程序订阅”。 


### <a name="usage"></a>使用情况  
<!-- TBD - IS ASDK --> 
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，不能使用此数据来精确地统计公共 IP 地址的使用情况。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下载更新
可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1805 更新包。


## <a name="see-also"></a>另请参阅
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。