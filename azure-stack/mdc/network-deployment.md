---
title: 适用于 MDC 的 Azure Stack 网络部署
description: 了解 MDC 设备的 Azure Stack 网络部署。
author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 01/17/2020
ms.openlocfilehash: d57e4a276ea93a8be70eb2bba05bdb8b2a318924
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182871"
---
# <a name="network-deployment"></a>网络部署

本主题介绍对 TOR 开关、IP 地址分配和其他网络部署任务的访问权限。

## <a name="plan-configuration-deployment"></a>规划配置部署

下一部分介绍了权限和 IP 地址分配。

### <a name="physical-switch-access-control-list"></a>物理交换机访问控制列表

为了保护 Azure Stack 解决方案，我们在 TOR 交换机上实现了 (Acl) 的访问控制列表。 本部分介绍如何实现此安全性。 下表显示了 Azure Stack 解决方案中每个网络的源和目标：

![TOR 开关上的访问控制列表示意图](media/network-deployment/acls.png)

下表将 ACL 引用与 Azure Stack 网络相关联。

| BMC 管理内部                            | 流量仅限于内部。                                                                                                                                      |   |   |   |   |   |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|---|
| BMC 管理外部                            | ACL 允许除边框设备之外的访问权限。                                                                                                                            |   |   |   |   |   |
| 扩展存储管理                        | 扩展存储系统的专用管理接口                                                                                                           |   |   |   |   |   |
| 交换机管理                                  | 专用交换机管理接口。                                                                                                                                   |   |   |   |   |   |
| "Azure Stack 基础结构"                 | Azure Stack 基础结构服务和 VM 的、受限网络                                                                                                          |   |   |   |   |   |
| Azure Stack 基础结构公共 (PEP/ERCS)  | Azure Stack 受保护的终结点，紧急恢复控制台服务器。 客户可以打开 ACL 以允许流量流向其数据中心管理网络。                        |   |   |   |   |   |
| Tor1,Tor2 RouterIP                           | 交换机环回接口，用于 SLB 和交换机/路由器之间的 BGP 对等互连。 客户可自行决定如何将这些 Ip 从边框中关闭。 |   |   |   |   |   |
| 存储                                      | 不在区域外部路由的专用 Ip                                                                                                                             |   |   |   |   |   |
| 内部 Vip                                | 不在区域外部路由的专用 Ip                                                                                                                             |   |   |   |   |   |
| 公共 Vip                                  | 由网络控制器管理的租户网络地址空间。                                                                                                           |   |   |   |   |   |
| 公共管理员 Vip                            | 与 Internal-VIPs 和 Azure Stack 基础结构通信所需的租户池中的小部分地址                                                    |   |   |   |   |   |
| 允许的网络                           | 客户定义的网络。                                                                                                                                                 |   |   |   |   |   |
| 0.0.0.0                                      | 从 Azure Stack 0.0.0.0 的角度来看，边界设备。                                                                                                         |   |   |   |   |   |
| **该类**                                   | 已启用允许流量，但默认情况下会阻止 SSH 访问。                                                                                                           |   |   |   |   |   |
| **无路由**                                     | 路由不会传播到 Azure Stack 环境外。                                                                                                         |   |   |   |   |   |
| **MUX ACL**                                      | Azure Stack MUX Acl 已使用。                                                                                                                                       |   |   |   |   |   |
| **空值**                                          | 不属于 VLAN ACL。                                                                                                                                                 |   |   |   |   |   |
|                                              |                                                                                                                                                                           |   |   |   |   |   |

### <a name="ip-address-assignments"></a>IP 地址分配

在 "部署" 工作表中，系统会要求提供以下网络地址来支持 Azure Stack 部署过程。 部署团队使用 "部署工作表" 工具将 IP 网络分解为系统所需的所有小型网络。

在此示例中，我们将填充部署工作表的 "网络设置" 选项卡，其中包含以下值：

-   BMC 网络： 10.193.132.0/27

-   专用网络存储网络 & 内部 Vip： 11.11.128.0/20

-   基础结构网络： 12.193.130.0/24

-   公共虚拟 IP (VIP) 网络： 13.200.132.0/24

-   交换机基础结构网络： 10.193.132.128/26

当您运行 "部署工作表" 工具的生成函数时，它将在电子表格上创建两个新的选项卡。 第一个选项卡是子网摘要，并显示了如何拆分超网以创建系统所需的所有网络。 在下面的示例中，只有在此选项卡上找到的列的子集。实际结果包含列出的每个网络的更多详细信息：

| **机架** | **子网类型** | **名称**                                   | **IPv4 子网**   | **IPv4 地址** |
|----------|-----------------|--------------------------------------------|-------------------|--------------------|
| 边框   | P2P 链接        | P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 | 4                  |
| 边框   | P2P 链接        | P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 | 4                  |
| 边框   | P2P 链接        | P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 | 4                  |
| 边框   | P2P 链接        | P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 | 4                  |
| 边框   | P2P 链接        | P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 | 4                  |
| 边框   | P2P 链接        | P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 | 4                  |
| Rack1    | 环回        | Loopback0_Rack1_TOR1                       | 10.193.132.152/32 | 1                  |
| Rack1    | 环回        | Loopback0_Rack1_TOR2                       | 10.193.132.153/32 | 1                  |
| Rack1    | 环回        | Loopback0_Rack1_BMC                        | 10.193.132.154/32 | 1                  |
| Rack1    | P2P 链接        | P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 | 4                  |
| Rack1    | P2P 链接        | P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 | 4                  |
| Rack1    | VLAN            | BMCMgmt                                    | 10.193.132.0/27   | 32                 |
| Rack1    | VLAN            | SwitchMgmt                                 | 10.193.132.168/29 | 8                  |
| Rack1    | VLAN            | CL01-RG01-SU01-Storage                     | 11.11.128.0/25    | 128                |
| Rack1    | VLAN            | CL01-RG01-SU01                       | 12.193.130.0/24   | 256                |
| Rack1    | 其他           | CL01-RG01-SU01-VIP                        | 13.200.132.0/24   | 256                |
| Rack1    | 其他           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

第二个选项卡是 **IP 地址使用情况** ，它显示了如何使用 ip：

#### <a name="bmc-network"></a>BMC 网络

BMC 网络的超网至少需要一个/26 个网络。 网关将使用网络中的第一个 IP，并在机架中使用 BMC 设备。 硬件生命周期主机在此网络上分配了多个地址，可用于部署、监视和支持机架。 这些 Ip 分布在3组中： DVM、InternalAccessible 和 ExternalAccessible。

- 机架： Rack1         
- 名称： BMCMgmt      

| **分配到**      | **IPv4 地址** |
|----------------------|------------------|
| 网络              | 10.193.132.0     |
| 网关              | 10.193.132.1     |
| HLH-BMC              | 10.193.132.2     |
| AzS-Node01           | 10.193.132.3     |
| AzS-Node02           | 10.193.132.4     |
| AzS-Node03           | 10.193.132.5     |
| AzS-Node04           | 10.193.132.6     |
| ExternalAccessible-1 | 10.193.132.19    |
| ExternalAccessible-2 | 10.193.132.20    |
| ExternalAccessible-3 | 10.193.132.21    |
| ExternalAccessible-4 | 10.193.132.22    |
| ExternalAccessible-5 | 10.193.132.23    |
| InternalAccessible-1 | 10.193.132.24    |
| InternalAccessible-2 | 10.193.132.25    |
| InternalAccessible-3 | 10.193.132.26    |
| InternalAccessible-4 | 10.193.132.27    |
| InternalAccessible-5 | 10.193.132.28    |
| CL01-RG01-SU01-DVM00 | 10.193.132.29    |
| HLH-OS               | 10.193.132.30    |
| 广播            | 10.193.132.31    |

#### <a name="storage-network"></a>存储网络

存储网络是专用网络，不应在机架之外路由。 它是专用网络超网的前半部分，按下表所示的方式进行分发。 网关是子网中的第一个 IP。 用于内部 Vip 的第二半部分是由 Azure Stack SLB 管理的地址的专用池，不会显示在 "IP 地址使用情况" 选项卡上。这些网络支持 Azure Stack，并在 TOR 交换机上提供了 Acl，以防止在解决方案外部播发和/或访问这些网络。

- 机架： Rack1                                    
- 名称： CL01-RG01-SU01-Storage 

| **分配到**              | **IPv4 地址** |
|------------------------------|------------------|
| 网络                      | 11.11.128.0      |
| 网关                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| 广播                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Azure Stack 基础结构网络

基础结构网络超网需要 a/24 网络，并且在部署工作表工具运行后，这将继续为24。 网关将是子网中的第一个 IP。

- 机架： Rack1               
- 名称： CL01-RG01-SU01 

| **分配到**            | **IPv4 地址** |
|----------------------------|------------------|
| 网络                    | 12.193.130.0     |
| 网关                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| 广播                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>交换机基础结构网络

基础结构网络分为多个物理交换机基础结构使用的网络。 这不同于仅支持 Azure Stack 软件的 Azure Stack 基础结构。 交换机基础网络仅支持物理交换机基础结构。 基础支持的网络包括：

| **名称**                                   | **IPv4 子网**   |
|--------------------------------------------|-------------------|
| P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 |
| P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 |
| P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 |
| P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 |
| P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 |
| P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 |
| Loopback0_Rack1_TOR1                       | 10.193.132.152/32 |
| Loopback0_Rack1_TOR2                       | 10.193.132.153/32 |
| Loopback0_Rack1_BMC                        | 10.193.132.154/32 |
| P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 |
| P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 |
| SwitchMgmt                                 | 10.193.132.168/29 |
|                                            |                   |

-   点到点 (P2P) ：这些网络允许所有交换机之间的连接。 对于每个 P2P，子网大小为/30 个网络。 最低 IP 始终分配给堆栈上上游 (北方) 设备。

-   环回：这些地址是/32 网络，分配给机架中使用的每个交换机。 边界设备不会被分配回送，因为它们不应成为 Azure Stack 解决方案的一部分。

-   交换机管理或交换机管理：此/29 网络支持机架中交换机的专用管理接口。 将按如下所示分配 Ip。此表还可以在部署工作表的 "IP 地址使用情况" 选项卡上找到：

- 机架： Rack1      
- 名称： SwitchMgmt

| **分配到**  | **IPv4 地址** |
|------------------|------------------|
| 网络          | 10.193.132.168   |
| 网关          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| 广播        | 10.193.132.175   |

## <a name="prepare-environment"></a>准备环境

硬件生命周期主机映像确实包含用于生成物理网络交换机配置的必需 Linux 容器。

最新的合作伙伴部署工具包包含最新的容器映像。
当需要生成更新的交换机配置时，可以替换硬件生命周期主机上的容器映像。

下面是更新容器映像的步骤：

1.  下载容器映像

2.  替换位于以下位置的容器映像

## <a name="generate-configuration"></a>生成配置

在这里，我们将引导你完成生成 JSON 文件和网络交换机配置文件的步骤：

1.  打开部署工作表

2.  填写所有选项卡上的所有必填字段

3.  调用部署工作表上的 "生成" 函数。  
    将创建两个额外的选项卡，其中显示生成的 IP 子网和分配。

4.  查看数据和确认后，调用 "Export" 函数。  
    系统将提示你提供一个将在其中保存 JSON 文件的文件夹。

5.  使用 Invoke-SwitchConfigGenerator.ps1 执行容器。 此脚本需要一个提升的 PowerShell 控制台来执行，并要求执行以下参数。

    -   容器名称–将生成交换机配置的容器的名称。

    -   ConfigurationData –从部署工作表导出的文件的 ConfigurationData.js路径。

    -   OutputDirectory-输出目录的路径。

    -   脱机–通知脚本在脱机模式下运行。

    ```powershell
    C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline
    ```

当脚本完成时，它将生成一个 zip 文件，其中包含在工作表中使用的前缀。 

```console
C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Seconds : 2
Section : Validation
Step    : WindowsRequirement
Status  : True
Detail  : @{CurrentImage=10.0.18363.0}


Seconds : 2
Section : Validation
Step    : DockerService
Status  : True
Detail  : @{Status=Running}


Seconds : 9
Section : Validation
Step    : DockerSetup
Status  : True
Detail  : @{CPU=4; Memory=4139085824; OS=Docker Desktop; OSType=linux}


Seconds : 9
Section : Validation
Step    : DockerImage
Status  : True
Detail  : @{Container=generalonrampacr.azurecr.io/master:1.1910.78.1}


Seconds : 10
Section : Run
Step    : Container
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c; ExternalPort=32768}


Seconds : 38
Section : Generate
Step    : Config
Status  : True
Detail  : @{OutputFile=c:\temp\N22R19.zip}


Seconds : 38
Section : Exit
Step    : StopContainer
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c}
```

### <a name="custom-configuration"></a>自定义配置

可以修改 Azure Stack 交换机配置的一些环境设置。 你可以确定可以在模板中更改的设置。 本文介绍每项这样的可自定义设置，以及所做的更改对 Azure Stack 的具体影响。 这些设置包括密码更新、Syslog 服务器、SNMP 监视、身份验证，以及访问控制列表。

在部署 Azure Stack 解决方案期间，原始设备制造商 (OEM) 会为 TOR 和 BMC 创建并应用交换机配置。 OEM 使用 Azure Stack 自动化工具来验证所需的配置是否已在这些设备上正确设置。 配置基于 Azure Stack 部署工作表中的信息。 

>[!NOTE]
>**不要**  无需 OEM 或 Microsoft Azure Stack 工程团队同意即可更改配置。 更改网络设备配置可能会显著影响 Azure Stack 实例中网络问题的操作或排查。 有关网络设备上这些功能的详细信息，如何进行这些更改，请联系 OEM 硬件提供商或 Microsoft 支持部门。 OEM 根据你的 Azure Stack 部署工作表通过自动化工具创建配置文件。 

但是，网络交换机的配置上的某些值是可以添加、删除或更改的。

#### <a name="password-update"></a>密码更新

操作员可以随时为网络交换机上的任何用户更新密码。 不需更改 Azure Stack 系统上的任何信息，也不需使用在 Azure Stack 中轮换机密所需的步骤。

#### <a name="syslog-server"></a>Syslog 服务器

操作员可以将交换机日志重定向到其数据中心的 Syslog 服务器。
使用此配置可确保特定时间点的日志可以用来进行故障排除。 默认情况下，日志存储在交换机上；交换机用于存储日志的容量有限。 请查看访问控制列表更新部分，大致了解如何配置进行交换机管理访问所需的权限。

#### <a name="snmp-monitoring"></a>SNMP 监视

操作员可以配置简单网络管理协议 (SNMP) v2 或 v3，以便监视网络设备并向数据中心的网络监视应用程序发送陷阱。 出于安全考虑，请使用 SNMPv3，因为它比 v2 更安全。 对于所需的 MIB 和配置，请咨询 OEM 硬件提供商。
请查看访问控制列表更新部分，大致了解如何配置进行交换机管理访问所需的权限。

#### <a name="authentication"></a>身份验证

操作员可以配置 RADIUS 或 TACACS，以便管理网络设备上的身份验证。 对于所需的受支持的方法和配置，请咨询 OEM 硬件提供商。 请查看访问控制列表更新部分，大致了解如何配置进行交换机管理访问所需的权限。

#### <a name="access-control-list-updates"></a>访问控制列表更新

操作员可以更改某些访问控制列表 (ACL)，允许从一系列受信任的数据中心网络访问网络设备管理接口和硬件生命周期主机 (HLH)。 操作员可以选择允许访问哪个组件以及允许从何处进行访问。 操作员可以通过访问控制列表允许特定网络范围中的管理 Jumpbox VM 访问交换机管理接口、HLH OS 和 HLH BMC。

有关更多详细信息，请参阅 [物理交换机访问控制列表](#physical-switch-access-control-list)。

#### <a name="tacacs-radius-and-syslog"></a>TACACS、RADIUS 和 Syslog

该 Azure Stack 解决方案将不附带用于访问控制设备（如交换机和路由器）的 TACACS 或 RADIUS 解决方案，也不会提供用于捕获交换机日志的 Syslog 解决方案，但所有这些设备都支持这些服务。
为了帮助集成到你的环境中的现有 TACACS、RADIUS 和/或 Syslog 服务器，我们将提供一个额外的文件，其中包含网络交换机配置，该配置将允许工程师现场自定义交换机以满足客户需求。

## <a name="next-steps"></a>后续步骤

网络集成
