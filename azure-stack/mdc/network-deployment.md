---
title: MDC 的 Azure Stack 网络部署
description: 了解 MDC 设备的 Azure Stack 网络部署。
author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 01/17/2020
ms.openlocfilehash: 8744ddd518094026afdba678d524ca981b138610
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598651"
---
# <a name="network-deployment"></a>网络部署

本主题介绍对 TOR 交换机的访问权限、IP 地址分配和其他网络部署任务。

## <a name="plan-configuration-deployment"></a>规划配置部署

后续部分介绍权限和 IP 地址分配。

### <a name="physical-switch-access-control-list"></a>物理交换机访问控制列表

为了保护 Azure Stack 解决方案，我们已在 TOR 交换机上实现了访问控制列表 (ACL)。 本部分介绍如何实现这一安全性。 下表显示了 Azure Stack 解决方案中每个网络的源和目标：

![TOR 交换机上访问控制列表的示意图](media/network-deployment/acls.png)

下表将 ACL 引用与 Azure Stack 网络相关联。

| BMC 内部管理                            | 流量仅限于内部。                                                                                                                                      |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BMC 外部管理                            | ACL 允许访问边界外的设备。                                                                                                                            |   |   |   |   |   |
| 扩展存储管理                        | 扩展存储系统的专用管理接口                                                                                                           |   |   |   |   |   |
| 交换机管理                                  | 专用交换机管理接口。                                                                                                                                   |   |   |   |   |   |
| Azure Stack 基础结构                 | Azure Stack 基础结构服务和 VM 的受限网络                                                                                                          |   |   |   |   |   |
| Azure Stack 公共基础结构 (PEP/ERCS) | Azure Stack 受保护的终结点，紧急恢复控制台服务器。 客户可以打开 ACL，以允许流量流向其数据中心管理网络。                        |   |   |   |   |   |
| Tor1、Tor2 路由器 IP                           | 交换机环回接口，用于 SLB 和交换机/路由器之间的 BGP 对等互连。 客户可自行决定是否在边界对这些 IP 关闭防火墙。 |   |   |   |   |   |
| 存储                                      | 不在区域外路由的专用 IP                                                                                                                             |   |   |   |   |   |
| 内部 VIP                                | 不在区域外路由的专用 IP                                                                                                                             |   |   |   |   |   |
| 公共 VIP                                  | 由网络控制器管理的租户网络地址空间。                                                                                                           |   |   |   |   |   |
| 公共管理员 VIP                            | 租户池中的一小部分地址，需要它们才能与内部 VIP 和 Azure Stack 基础结构通信                                                    |   |   |   |   |   |
| 允许的网络                           | 客户定义的网络。                                                                                                                                                 |   |   |   |   |   |
| 0.0.0.0                                      | 从 Azure Stack 的角度来看，0.0.0.0 是边界设备。                                                                                                         |   |   |   |   |   |
| 允许                                   | 已启用允许流量，但默认情况下会阻止 SSH 访问。                                                                                                           |   |   |   |   |   |
| 无路由                                     | 路由不会传播到 Azure Stack 环境外。                                                                                                         |   |   |   |   |   |
| MUX ACL                                      | 已在使用 Azure Stack MUX ACL。                                                                                                                                       |   |   |   |   |   |
| **不适用**                                          | 不属于 VLAN ACL。                                                                                                                                                 |   |   |   |   |   |
|                                              |                                                                                                                                                                           |   |   |   |   |   |

### <a name="ip-address-assignments"></a>IP 地址分配

在部署工作表中，系统会要求你提供以下网络地址来支持 Azure Stack 部署过程。 部署团队使用部署工作表工具将 IP 网络拆分为系统所需的所有小型网络。

在此示例中，我们将使用以下值填充部署工作表的“网络设置”选项卡：

-   BMC 网络：10.193.132.0 /27

-   专用网络存储网络和内部 VIP：11.11.128.0 /20

-   基础结构网络：12.193.130.0 /24

-   公共虚拟 IP (VIP) 网络：13.200.132.0 /24

-   交换机基础结构网络：10.193.132.128 /26

运行部署工作表工具的 Generate 函数时，它将在电子表格上创建两个新的选项卡。 第一个选项卡是“子网摘要”，它显示了如何拆分超网以创建系统所需的所有网络。 在下面的示例中，仅在此选项卡上找到一小部分列。实际结果包含列出的每个网络的更多详细信息：

| **机架** | **子网类型** | **Name**                                   | **IPv4 子网**   | **IPv4 地址** |
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
| Rack1    | VLAN            | CL01-RG01-SU01-Infra                       | 12.193.130.0/24   | 256                |
| Rack1    | 其他           | CL01-RG01-SU01-VIPS                        | 13.200.132.0/24   | 256                |
| Rack1    | 其他           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

第二个选项卡是“IP 地址使用情况”，它显示了 IP 的使用情况：

#### <a name="bmc-network"></a>BMC 网络

BMC 网络的超网至少需要一个 /26 网络。 网关将使用网络中的第一个 IP，然后使用机架中的 BMC 设备。 硬件生命周期主机在此网络上分配了多个地址，可用于部署和监视机架并为其提供支持。 这些 IP 分为 3 组：DVM、InternalAccessible 和 ExternalAccessible。

- 机架：Rack1         
- 姓名：BMCMgmt      

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

存储网络是专用网络，不应在机架之外路由。 它是专用网络超网的前半部分，按下表所示的方式进行分发。 网关是子网中的第一个 IP。 用于内部 VIP 的后半部分是由 Azure Stack SLB 管理的地址的专用池，它不会显示在“IP 地址使用情况”选项卡上。这些网络支持 Azure Stack，并且 TOR 交换机上提供了防止在解决方案外播发和/或访问这些网络的 ACL。

- 机架：Rack1                                    
- 姓名：CL01-RG01-SU01-Storage 

| **分配到**              | **IPv4 地址** |
|------------------------------|------------------|
| 网络                      | 11.11.128.0      |
| 网关                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| 广播                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Azure Stack 基础结构网络

基础结构网络超网需要一个 /24 网络，在部署工作表工具运行后，该网络仍然是 /24。 网关将是子网中的第一个 IP。

- 机架：Rack1               
- 姓名：CL01-RG01-SU01-Infra 

| **分配到**            | **IPv4 地址** |
|----------------------------|------------------|
| 网络                    | 12.193.130.0     |
| 网关                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| 广播                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>交换机基础结构网络

基础结构网络分为多个由物理交换机基础结构使用的网络。 这不同于仅支持 Azure Stack 软件的 Azure Stack 基础结构。 交换机基础结构网络仅支持物理交换机基础结构。 基础结构支持的网络包括：

| **Name**                                   | **IPv4 子网**   |
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

-   点到点 (P2P)：这些网络允许所有交换机之间的连接。 对于每个 P2P，子网大小是一个 /30 网络。 最低 IP 始终分配给堆栈上的上游（北部）设备。

-   环回：这些地址是分配给机架中使用的每个交换机的 /32 网络。 系统不会为边界设备分配环回，因为它们不应为 Azure Stack 解决方案的一部分。

-   交换机管理 (Switch Mgmt)：这个 /29 网络支持机架中的交换机的专用管理接口。 系统将按如下所示分配 IP；你还可在部署工作表的“IP 地址使用情况”选项卡上找到此表：

- 机架：Rack1      
- 姓名：SwitchMgmt

| **分配到**  | **IPv4 地址** |
|------------------|------------------|
| 网络          | 10.193.132.168   |
| 网关          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| 广播        | 10.193.132.175   |

## <a name="prepare-environment"></a>准备环境

硬件生命周期主机映像确实包含用于生成物理网络交换机配置所必需的 Linux 容器。

最新的合作伙伴部署工具包包含最新的容器映像。
如果需要生成更新的交换机配置，可以替换硬件生命周期主机上的容器映像。

下面是更新容器映像的步骤：

1.  下载容器映像

2.  替换位于以下位置的容器映像

## <a name="generate-configuration"></a>生成配置

在这里，我们将引导你完成生成 JSON 文件和网络交换机配置文件的步骤：

1.  打开部署工作表

2.  填写所有选项卡上的所有必填字段

3.  调用部署工作表上的“Generate”函数。  
    此时将创建两个额外的选项卡，其中显示生成的 IP 子网和分配。

4.  查看数据，确认后，调用“Export”函数。  
    系统将提示你提供一个要保存 JSON 文件的文件夹。

5.  使用 Invoke-SwitchConfigGenerator.ps1 执行容器。 此脚本需要一个提升的 PowerShell 控制台和以下参数才能执行。

    -   ContainerName - 将生成交换机配置的容器的名称。

    -   ConfigurationData - 从部署工作表导出的 ConfigurationData.json 文件的路径。

    -   OutputDirectory - 输出目录的路径。

    -   Offline - 指示脚本以脱机模式运行。

    ```powershell
    C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline
    ```

当脚本完成时，它将生成一个带有工作表中使用的前缀的 zip 文件。 

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

可以修改 Azure Stack 交换机配置的一些环境设置。 可确定能够在模板中更改哪些设置。 本文介绍每项这样的可自定义设置，以及所做的更改对 Azure Stack 的具体影响。 这些设置包括密码更新、Syslog 服务器、SNMP 监视、身份验证，以及访问控制列表。

在部署 Azure Stack 解决方案期间，原始设备制造商 (OEM) 会为 TOR 和 BMC 创建并应用交换机配置。 OEM 使用 Azure Stack 自动化工具来验证所需的配置是否已在这些设备上正确设置。 配置基于 Azure Stack 部署工作表中的信息。 

>[!NOTE]
>**请勿** 在 OEM 或 Microsoft Azure Stack 工程团队同意的情况下更改配置。 更改网络设备配置可能会显著影响 Azure Stack 实例中网络问题的操作或排查。 有关网络设备上这些功能的详细信息，如何进行这些更改，请联系 OEM 硬件提供商或 Microsoft 支持部门。 OEM 根据你的 Azure Stack 部署工作表通过自动化工具创建配置文件。 

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

如需了解更多详细信息，请参阅[物理交换机访问控制列表](#physical-switch-access-control-list)。

#### <a name="tacacs-radius-and-syslog"></a>TACACS、RADIUS 和 Syslog

Azure Stack 解决方案不会附带用于交换机和路由器等设备的访问控制的 TACACS 或 RADIUS 解决方案，也不会附带用于捕获交换机日志的 Syslog 解决方案，但所有这些设备都支持这些服务。
为了帮助集成到你的环境中的现有 TACACS、RADIUS 和/或 Syslog 服务器，我们将提供一个额外的文件，其中包含网络交换机配置，该配置将允许工程师现场自定义交换机以满足客户需求。

## <a name="next-steps"></a>后续步骤

网络集成
