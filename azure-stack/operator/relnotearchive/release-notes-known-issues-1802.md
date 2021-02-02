---
title: Azure Stack 1802 更新 |Microsoft Docs
description: 了解 Azure Stack 集成系统 1802 更新的功能、已知问题和更新下载位置。
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
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: b9334a3079a767f723240946af8091508204b8ae
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248020"
---
# <a name="azure-stack-1802-update"></a>Azure Stack 1802 更新

*适用于：Azure Stack 集成系统*

本文介绍 1802 更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]        
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考    
Azure Stack 1802 更新内部版本号为 **20180302.1**。  


## <a name="before-you-begin"></a>在开始之前    
> [!IMPORTANT]    
> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。


### <a name="prerequisites"></a>必备条件
- 在应用 Azure Stack 1802 更新之前，请安装 Azure Stack 1712 更新。    

- 在应用 Azure Stack 1802 更新之前，请安装 **AzS 修补程序 1.0.180312.1-Build 20180222.2** 。 此修补程序更新了 Windows Defender，在下载 Azure Stack 的更新后即可使用。

  若要安装此修补程序，请执行[安装 Azure Stack 的更新](../azure-stack-apply-updates.md)所需的常规过程。 更新的名称显示为 **AzS 修补程序 1.0.180312.1**，并包括以下文件： 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  将这些文件上传到存储帐户和容器以后，请在管理门户的“更新”磁贴中运行安装。 
  
  与 Azure Stack 的更新不同，此更新的安装不更改 Azure Stack 的版本。  若要确认此更新是否已安装，请查看“已安装更新”列表。
 


### <a name="post-update-steps"></a>更新后步骤
安装 1802 之后，请安装任何适用的修补程序。 有关详细信息，请查看以下知识库文章，以及我们的[服务策略](../azure-stack-servicing-policy.md)。 
- Azure Stack 修补程序 **1.0.180302.4**。 [KB 4131152 - 现有虚拟机规模集可能不可用]( https://support.microsoft.com/help/4131152) 

  此修补程序还可解决 [KB 4103348 - 尝试安装 Azure Stack 更新时，网络控制器 API 服务崩溃](https://support.microsoft.com/help/4103348)中详述的问题。


### <a name="new-features-and-fixes"></a>新功能和修复
此更新包含以下适用于 Azure Stack 的改进和修复。

- **增加了对以下 Azure 存储服务 API 版本的支持**：
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    有关详细信息，请参阅 [Azure Stack 存储：差异和注意事项](../../user/azure-stack-acs-differences.md)。

- **对更大型 [块 Blob](../../user/azure-stack-acs-differences.md) 的支持**：
    - 允许的最大块大小从 4 MB 增至 100 MB。
    - 最大 Blob 大小从 195 GB 增至 4.75 TB。  

- **基础结构备份** 现在在“资源提供程序”磁贴中显示。备份警报已启用。 有关基础结构备份服务的详细信息，请参阅[使用基础结构备份服务对 Azure Stack 进行备份和数据恢复](../azure-stack-backup-infrastructure-backup.md)。

- **对 *Test-AzureStack* cmdlet 的更新**，用于改进存储诊断。 有关此 cmdlet 的详细信息，请参阅[针对 Azure Stack 的验证](../azure-stack-diagnostic-test.md)。

- **基于角色的访问控制 (RBAC) 改进** - 现在，当 Azure Stack 通过 AD FS 进行部署时，可以使用 RBAC 将权限委托给通用用户组。 若要详细了解 RBAC，请参阅[管理 RBAC](../azure-stack-manage-permissions.md)。

- **增加了对多个容错域的支持**。  有关详细信息，请参阅 Azure Stack 的高可用性。

- **支持物理内存升级** - 现在可以在初始部署后扩展 Azure Stack 集成系统的内存容量。 有关详细信息，请参阅[管理 Azure Stack 的物理内存容量](../azure-stack-manage-storage-physical-memory-capacity.md)。

- Azure Stack 所使用的性能、稳定性、安全性和操作系统的 **各种修补程序**。

<!-- #### New features -->

<!-- #### Fixes -->

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题    
更新 1802 的安装没有任何已知问题。


### <a name="known-issues-post-installation"></a>已知问题（安装后）
下面是内部版本 **20180302.1** 的安装后已知问题

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

-   在管理门户中，可能会看到针对 Microsoft.Update.Admin 组件的严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

    可以放心地忽略此警报。 

<!-- 2253274 --> 
-  在管理员和用户门户中，vNet 子网的“设置”边栏选项卡无法加载。 解决方法是使用 PowerShell 和 [new-azurermvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) cmdlet 来查看和管理此信息。

- 在管理员门户和用户门户中，如果选择通过旧版 API（例如 2015-06-15）创建的存储帐户的“概述”边栏选项卡，则“概述”边栏选项卡无法加载。 这包括系统存储帐户，例如修补和更新过程中使用的 **updateadminaccount**。 

  解决方法是使用 PowerShell 运行 **Start-ResourceSynchronization.ps1** 脚本，以便重新可以访问存储帐户详细信息。 [GitHub 中提供了该脚本](https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)，必须在特权终结点上使用服务管理员凭据运行该脚本。 

- “服务运行状况”边栏选项卡无法加载。 在管理员或用户门户中打开“服务运行状况”边栏选项卡时，Azure Stack 显示错误且不加载信息。 这是预期行为。 尽管可以选择并打开“服务运行状况”，但此功能目前不可用，将来的 Azure Stack 版本中会实现此功能。


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

<!-- 2290877  --> 
-  不能纵向扩展使用 1802 之前的 Azure Stack 版本时创建的虚拟机规模集 (VMSS)。 这是由于将可用性集与虚拟机规模集配合使用时的支持发生了变化。 此支持是通过版本 1802 添加的。  对于在添加此支持之前创建的 VMSS，若要尝试通过添加更多的实例对其进行缩放，则操作会失败，并出现消息“预配状态为‘已失败’”。 

  版本 1803 中已解决此问题。 若要在版本 1802 中解决此问题，请安装 Azure Stack 修补程序 **1.0.180302.4**。 有关详细信息，请参阅 [KB 4131152：现有虚拟机规模集可能不可用](https://support.microsoft.com/help/4131152)。 

- Azure Stack 支持只使用固定类型的 VHD。 某些通过 Azure Stack 上的市场提供的映像使用动态 VHD，但这些映像已删除。 重设附加了动态磁盘的虚拟机 (VM) 的大小会导致该 VM 处于故障状态。

  若要缓解此问题，请删除 VM，而不要删除 VM s 磁盘（存储帐户中的 VHD blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，接着重新创建虚拟机。

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

- 内部负载均衡 (ILB) 对后端 VM 的 MAC 地址的处理不恰当，导致在后端网络上使用 Linux 实例时无法维持 ILB。  ILB 适用于后端网络上的 Windows 实例。

-   IP 转发功能在门户中可见，但启用 IP 转发却没有效果。 尚不支持此功能。

-  Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- 在使用 *自动* 的 DNS 服务器设置创建的虚拟网络上，更改为自定义 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 VM。

- Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。

<!-- 2096388 --> 
- 不能使用管理员门户更新网络安全组的规则。 

    针对应用服务的解决方法：如需通过远程桌面连接到控制器实例，请使用 PowerShell 修改网络安全组中的安全规则。  以下示例说明了如何先将配置设置为 *allow*，然后再还原为 *deny*：  
    
    - *禁止*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
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
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
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
> 更新到 Azure Stack 1802 以后，可以继续使用以前部署的 SQL 和 MySQL 资源提供程序。  建议在新版本发布后更新 SQL 和 MySQL。 与 Azure Stack 一样，请将更新按顺序应用到 SQL 和 MySQL 资源提供程序。  例如，如果使用版本 1710，请先应用版本 1711，然后应用 1712，再应用 1802 的更新。      
>   
> 安装更新 1802 不会影响用户现在使用 SQL 或 MySQL 资源提供程序。
> 不管所用资源提供程序的版本如何，在其数据库中的用户数据不会受到影响，仍然可用。    


#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

  若要解决此问题，请将 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 添加到脚本顶部，强制 PowerShell 控制台在从 GitHub 存储库下载项目时使用 TLSv1.2。


## <a name="download-the-update"></a>下载更新
可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1802 更新包。


## <a name="more-information"></a>更多信息
Microsoft 已提供某种方式让用户使用装有 1710 更新的特权终结点 (PEP) 来监视和恢复更新。

- 请参阅文档[使用特权终结点监视 Azure Stack 中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。

## <a name="see-also"></a>另请参阅

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。