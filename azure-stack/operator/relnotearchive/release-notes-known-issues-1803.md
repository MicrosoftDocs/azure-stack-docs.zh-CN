---
title: Azure Stack 1803 更新 |Microsoft Docs
description: 了解 Azure Stack 集成系统 1803 更新的功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6f15453ce20988929728a9d326ab5f7bf901beca
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248013"
---
# <a name="azure-stack-1803-update"></a>Azure Stack 1803 更新

*适用于：Azure Stack 集成系统*

本文介绍 1803 更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]        
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考    
Azure Stack 1803 更新内部版本号为 **20180329.1**。


## <a name="before-you-begin"></a>在开始之前    
> [!IMPORTANT]    
> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。


### <a name="prerequisites"></a>必备条件
- 在应用 Azure Stack 1803 更新之前，请安装 Azure Stack 1802 更新。   

- 在应用 Azure Stack 1803 更新之前，请安装 **AzS 修补程序 1.0.180312.1-Build 20180222.2** 。 此修补程序更新了 Windows Defender，在下载 Azure Stack 的更新后即可使用。

  若要安装此修补程序，请执行[安装 Azure Stack 的更新](../azure-stack-apply-updates.md)所需的常规过程。 更新的名称显示为 **AzS 修补程序 1.0.180312.1**，并包括以下文件： 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  将这些文件上传到存储帐户和容器以后，请在管理门户的“更新”磁贴中运行安装。 
  
  与 Azure Stack 的更新不同，此更新的安装不更改 Azure Stack 的版本。 若要确认此更新是否已安装，请查看“已安装更新”列表。



### <a name="new-features"></a>新增功能 
此更新包含以下适用于 Azure Stack 的改进和修复。

- **更新 Azure Stack 机密** -（帐户和证书）。 有关如何管理机密的详细信息，请参阅[在 Azure Stack 中轮换机密](../azure-stack-rotate-secrets.md)。 

<!-- 1914853 --> 
-  使用 HTTP 访问管理员和用户门户时 **自动重定向到 HTTPS**。 进行此改进是基于 Azure Stack 的 [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) 反馈。 

<!-- 2202621  --> 
- **访问 marketplace** 你现在可以使用 "管理" 和 "用户" 门户中的 " [+ 新建](https://ms.portal.azure.com/#create/hub) " 选项来打开 Azure Stack marketplace，就像在 Azure 门户中一样。
 
<!-- 2202621 --> 
- **Azure Monitor** - Azure Stack 向管理员和用户门户添加了 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。 这包括用于指标和活动日志的新资源管理器。 若要从外部网络访问此 Azure Monitor，必须在防火墙配置中打开端口 **13012**。 有关 Azure Stack 所需端口的详细信息，请参阅 [Azure Stack 数据中心集成 - 发布终结点](../azure-stack-integrate-endpoints.md)。

   另外，“更多服务”下的“审核日志”现在显示为“活动日志”，这也是此次更改的内容。 此功能现在与 Azure 门户一致。 

<!-- 1664791 --> 
- **稀疏文件** - 向 Azure Stack 添加新映像时，或者通过市场联合添加映像时，映像会转换为稀疏文件。 在使用 Azure Stack 版本 1803 之前添加的映像不能进行转换， 而只能使用市场联合重新提交这些映像，以便利用此功能。 
 
   稀疏文件是一种文件格式，在减少存储空间占用并提高 I/O 方面很有效。 ?有关详细信息，请参阅 [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) For Windows Server。 

### <a name="fixed-issues"></a>已修复的问题

<!-- 1739988 --> 
-  内部负载均衡 (ILB) 现在可以正确地处理后端 VM 的 MAC 地址，使 ILB 可以在后端网络上使用 Linux 实例时将数据包放置到后端网络。 ILB 适用于后端网络上的 Windows 实例。 

<!-- 1805496 --> 
-  因 Azure Stack 使用的 IKE 策略设置不同于 Azure 所用的而导致 Azure Stack 之间的 VPN 连接断开的问题。 SALifetime（时间）和 SALiftetime（字节）的值与 Azure 不兼容，在 1803 中已更改，以便与 Azure 设置匹配。 在低于 1803 的版本中，SALifetime（秒）的值为 14,400，在版本 1803 中已更改为 27,000。 在低于 1803 的版本中，SALifetime（字节）的值为 819,200，在版本 1803 中已更改为 33,553,408。

<!-- 2209262 --> 
-  IP 问题，具体表现在 VPN 连接以前在门户中可见，但启用或切换“IP 转发”却没有效果。 此功能默认启用，更改此项的功能尚不受支持。  此控件已从门户中删除。 

<!-- 1766332 --> 
-  Azure Stack 不支持“基于策略的 VPN 网关”，即使此选项显示在门户中。  此选项已从门户中删除。 

<!-- 1868283 --> 
-  Azure Stack 现在阻止重设使用动态磁盘创建的虚拟机的大小。 

<!-- 1756324 --> 
-  虚拟机的使用情况数据现在按“小时”时间间隔进行划分。 这与 Azure 一致。 

<!--  2253274 --> 
-  管理员和用户门户中 vNet 子网的“设置”边栏选项卡无法加载的问题。 解决方法是使用 PowerShell 和 [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) cmdlet 来查看和管理此信息。

- 在创建虚拟机时，“无法显示定价”消息不再在选择某个大小作为 VM 大小时显示。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的各种修复。


### <a name="changes"></a>更改
- 将新创建的产品/服务的状态从“专用”更改为“公用”或“停止使用”的方式已变。 有关详细信息，请参阅[创建套餐](../azure-stack-create-offer.md)。


### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题    
<!-- 2328416 -->  在安装 1803 更新期间，Blob 服务以及使用 Blob 服务的内部服务可能无法使用。 这包括某些虚拟机操作。 此停机时间可能会导致无法访问数据的来自服务的租户操作或警报失败。 当更新安装完以后，此问题会自行解决。 



### <a name="post-update-steps"></a>更新后步骤
- 安装 1803 之后，请安装任何适用的修补程序。 有关详细信息，请查看以下知识库文章，以及我们的[服务策略](../azure-stack-servicing-policy.md)。

  - [KB 4344115 - Azure Stack 修补程序 1.0.180427.15](https://support.microsoft.com/help/4344115)。

- 安装此更新后，请查看防火墙配置以确保[必需的端口](../azure-stack-integrate-endpoints.md)处于打开状态。 例如，此更新引入了 Azure Monitor，其中包括将审核日志更改为活动日志。 由于此更改，端口 13012 现在已使用，并且也必须处于打开状态。  


### <a name="known-issues-post-installation"></a>已知问题（安装后）
下面是内部版本 **20180323.2** 的安装后已知问题。

#### <a name="portal"></a>门户
<!-- 2332636 - IS -->  
- 对 Azure Stack 标识系统使用 AD FS 并更新到此版本的 Azure Stack 时，默认提供程序订阅的默认所有者将重置为内置的 **CloudAdmin** 用户。  
  解决方法：若要在安装此更新后解决该问题，请使用[触发自动化以便在 Azure Stack 中配置声明提供程序信任](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub)过程中的步骤 3 来重置默认提供程序订阅的所有者。   

- 从管理门户内 [的下拉列表中打开新支持请求](../azure-stack-manage-portals.md#quick-access-to-help-and-support) 的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 集成系统，请使用 https://aka.ms/newsupportrequest。

<!-- 2050709 --> 
-  在管理员门户中，不能编辑 Blob 服务、表服务或队列服务的存储指标。 转到“存储”并选择 Blob、表或队列服务磁贴后，就会打开一个新的边栏选项卡，其中显示该 服务的指标图表。 如果随后从指标图表磁贴顶部选择“编辑”，则会打开“编辑图表”边栏选项卡，但其中不显示用于编辑指标的选项。

- 无法在管理员门户中查看计算或存储资源。 此问题的原因是更新安装过程中出错，导致系统错误地将更新报告为成功。 如果发生此问题，请联系 Microsoft 客户支持服务部门以寻求帮助。

- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

- 在管理门户的仪表板中，“更新”磁贴无法显示有关更新的信息。 若要解决此问题，请单击该磁贴对其进行刷新。

- 在管理门户中，可能会看到针对 *Microsoft.Update.Admin* 组件的严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

  可以放心地忽略此警报。 


#### <a name="health-and-monitoring"></a>运行状况和监视
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

  可以放心地忽略这两个警报。 它们将在一段时间后自动关闭。  


#### <a name="marketplace"></a>市场
- 用户无需订阅就能浏览整个市场，并且能看到计划和套餐等管理项。 对用户而言，这些项是非功能性的。



#### <a name="compute"></a>计算
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

-  通过转到“新建” > **“计算”** > “可用性集”在门户中创建可用性集时，只能创建 1 个包含 1 个容错域和 1 个更新域的可用性集。 解决方法是在创建新的虚拟机时，通过 PowerShell、CLI 或门户来创建可用性集。

- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 D 系列 VM 的数据磁盘数不正确。 所有受支持的 D 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

-  如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  


#### <a name="networking"></a>网络
- 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。



-  Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- 在使用 *自动* 的 DNS 服务器设置创建的虚拟网络上，更改为自定义 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 VM。

- Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。

<!-- 2096388 --> 
- 不能使用管理员门户更新网络安全组的规则。 

    针对应用服务的解决方法：如需通过远程桌面连接到控制器实例，请使用 PowerShell 修改网络安全组中的安全规则。  以下示例说明了如何先将配置设置为 *allow*，然后再还原为 *deny*：  
    
    - *禁止*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn�t work. Need to set properties again even in case of edit
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn�t work. Need to set properties again even in case of edit
    
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


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
- 在继续操作之前，请查看这些发行说明开头附近的[开始之前](#before-you-begin)中的重要说明。

- 可能需要在长达一小时的时间过后，用户才能在新的 SQL 或 MySQL 部署中创建数据库。

- 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。  

<!-- IS, ASDK --> 
- 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列** 名称中不支持使用特殊字符（包括空格和句点）。

> [!NOTE]  
> 更新到 Azure Stack 1803 以后，可以继续使用以前部署的 SQL 和 MySQL 资源提供程序。  建议在新版本发布后更新 SQL 和 MySQL。 与 Azure Stack 一样，请将更新按顺序应用到 SQL 和 MySQL 资源提供程序。  例如，如果使用版本 1711，请先应用版本 1712，然后应用 1802,，再应用 1803 的更新。      
>   
> 安装更新 1803 不会影响用户现在使用 SQL 或 MySQL 资源提供程序。
> 不管所用资源提供程序的版本如何，在其数据库中的用户数据不会受到影响，仍然可用。    



#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。


#### <a name="usage"></a>使用情况  
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，你可以使用此数据来精确地统计公共 IP 地址的使用情况。

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

  若要解决此问题，请将 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 添加到脚本顶部，强制 PowerShell 控制台在从 GitHub 存储库下载项目时使用 TLSv1.2。


## <a name="download-the-update"></a>下载更新
可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1803 更新包。


## <a name="see-also"></a>另请参阅
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。