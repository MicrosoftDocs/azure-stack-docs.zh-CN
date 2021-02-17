---
title: 使用数据中心防火墙通过 PowerShell 配置 Acl
description: 使用本主题来了解 Azure Stack HCI、Windows Server 2019 和 Windows Server 2016 中的软件定义网络的数据中心防火墙入门。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/09/2021
ms.openlocfilehash: 38f7511d4ccc11c4801abde82ec8ab1c7091eb69
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562649"
---
# <a name="use-datacenter-firewall-to-configure-acls-with-powershell"></a>使用数据中心防火墙通过 PowerShell 配置 Acl

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019、Windows Server 2016

本主题介绍了如何通过 Windows PowerShell 将[数据中心防火墙](../concepts/datacenter-firewall-overview.md)用于 Azure Stack HCI 中软件定义的网络 (SDN)，以便配置访问控制列表 (ACL) 来管理数据通信流。 可以通过创建应用于子网或网络接口的 ACL 来启用和配置数据中心防火墙。 本主题中的示例脚本使用从 NetworkController 模块导出的 Windows PowerShell 命令。 你还可以使用 Windows 管理中心来配置和管理 ACL。

## <a name="configure-datacenter-firewall-to-allow-all-traffic"></a>配置数据中心防火墙以允许所有流量

部署 SDN 后，应在新环境中针对基本网络连接性进行测试。 为实现此目的，请为数据中心防火墙创建一个规则，以允许所有网络流量，不设限制。

使用下表中的条目创建一组规则，以允许所有入站和出站网络流量。

| 源 IP | 目标 IP | 协议 | Source Port | Destination Port | 方向 | 操作 | 优先级 |
|:---------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|    \*     |       \*       |   All    |     \*      |        \*        |  入站  | Allow  |   100    |
|    \*     |       \*       |   All    |     \*      |        \*        | 出站  | Allow  |   110    |

---

在此示例中，你将创建具有两个规则的 ACL：

1. AllowAll_Inbound - 允许所有网络流量进入配置了此 ACL 的网络接口。
2. AllowAllOutbound - 允许所有流量从网络接口传出。 由资源 ID“AllowAll-1”标识的此 ACL 现在已就绪，可在虚拟子网和网络接口中使用。

首先，通过打开 PowerShell 会话连接到群集节点之一：

```PowerShell
Enter-PSSession <server-name>
```

然后，运行以下脚本来创建 ACL：

```PowerShell
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"
$aclrule1 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule1.Properties = $ruleproperties
$aclrule1.ResourceId = "AllowAll_Inbound"
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "110"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"
$aclrule2 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule2.Properties = $ruleproperties
$aclrule2.ResourceId = "AllowAll_Outbound"
$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = @($aclrule1, $aclrule2)
New-NetworkControllerAccessControlList -ResourceId "AllowAll" -Properties $acllistproperties -ConnectionUri <NC REST FQDN>
```

>[!NOTE]
>适用于网络控制器的 Windows PowerShell 命令参考位于[网络控制器 cmdlet](/powershell/module/networkcontroller/) 主题中。

## <a name="use-acls-to-limit-traffic-on-a-subnet"></a>使用 ACL 限制子网上的流量
在此示例中，你将创建一个 ACL，用于阻止 192.168.0.0/24 子网中的虚拟机 (VM) 相互通信。 此类型的 ACL 可用于限制攻击者在子网内横向攻击的能力，同时仍允许 VM 接收来自子网外部的请求，以及与其他子网上的其他服务进行通信。

|   源 IP    | 目标 IP | 协议 | Source Port | Destination Port | 方向 | 操作 | 优先级 |
|:--------------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|  192.168.0.1   |       \*       |   All    |     \*      |        \*        |  入站  | Allow  |   100    |
|       \*       |  192.168.0.1   |   All    |     \*      |        \*        | 出站  | Allow  |   101    |
| 192.168.0.0/24 |       \*       |   All    |     \*      |        \*        |  入站  | 阻止  |   102    |
|       \*       | 192.168.0.0/24 |   All    |     \*      |        \*        | 出站  | 阻止  |   103    |
|       \*       |       \*       |   All    |     \*      |        \*        |  入站  | Allow  |   104    |
|       \*       |       \*       |   All    |     \*      |        \*        | 出站  | Allow  |   105    |

---

由下面的示例脚本创建的 ACL（由资源 ID Subnet-192-168-0-0 标识）现在可以应用于使用“192.168.0.0/24”子网地址的虚拟网络子网。 附加到该虚拟网络子网的任何网络接口都会自动应用上述 ACL 规则。

下面是一个示例脚本，用于通过网络控制器 REST API 创建此 ACL：

```PowerShell
import-module networkcontroller
$ncURI = "https://mync.contoso.local"
$aclrules = @()

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "192.168.0.1"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.1"
$ruleproperties.Priority = "101"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Outbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "192.168.0.0/24"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "102"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.0/24"
$ruleproperties.Priority = "103"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Outbound"

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "104"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "105"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Outbound"
$aclrules += $aclrule

$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = $aclrules

New-NetworkControllerAccessControlList -ResourceId "Subnet-192-168-0-0" -Properties $acllistproperties -ConnectionUri $ncURI
```

## <a name="add-an-acl-to-a-network-interface"></a>将 ACL 添加到网络接口

创建 ACL 并将其分配给虚拟子网后，你可能希望将虚拟子网上的默认 ACL 替代为单个网络接口的特定 ACL。 从 Windows Server 2019 Datacenter 开始，除了 SDN 虚拟网络外，还可以将特定 Acl 直接应用于附加到 SDN 逻辑网络的网络接口。 如果在连接到网络接口的虚拟子网上设置了 Acl，则会应用这两个 Acl，并且网络接口 Acl 优先于虚拟子网 Acl。

在此示例中，我们演示如何将 ACL 添加到虚拟网络。

>[!TIP]
>还可以在创建网络接口的同时添加 ACL。

1. 获取或创建要将 ACL 添加到其中的网络接口。

   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. 获取或创建将添加到网络接口的 ACL。

   ```PowerShell
   $acl = get-networkcontrolleraccesscontrollist -ConnectionUri $uri -ResourceId "AllowAllACL"
   ```

3. 将 ACL 分配到该网络接口的 AccessControlList 属性。

   ```PowerShell
    $nic.properties.ipconfigurations[0].properties.AccessControlList = $acl
   ```

4. 在网络控制器中添加网络接口。

   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="remove-an-acl-from-a-network-interface"></a>从网络接口中删除 ACL

在此示例中，我们展示如何从网络接口中删除 ACL。 删除 ACL 会将默认的规则集应用于网络接口。 默认规则集允许所有出站流量，但阻止所有入站流量。 如果要允许所有入站流量，则必须按照前面的[示例](#add-an-acl-to-a-network-interface)添加允许所有入站和出站流量的 ACL。

1. 获取将从中删除 ACL 的网络接口。
   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. 将 $null 分配到 ipConfiguration 的 AccessControlList 属性。
   ```PowerShell
   $nic.properties.ipconfigurations[0].properties.AccessControlList = $null
   ```

3. 在网络控制器中添加网络接口对象。
   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="firewall-auditing"></a>防火墙审核

防火墙审核是在 Windows Server 2019 中引入的，它是数据中心防火墙的一项新功能，用于记录 SDN 防火墙规则处理的任何流。 将记录已启用日志记录的所有 ACL。 日志文件必须采用与 [Azure 网络观察程序流日志](/azure/network-watcher/network-watcher-nsg-flow-logging-overview)一致的语法。 可以使用这些日志进行诊断，也可以将其存档供以后分析。

下面是一个示例脚本，用于在主机服务器上启用防火墙审核。 请更新开头的变量，并在部署了[网络控制器](../concepts/network-controller-overview.md)的 Azure Stack HCI 群集上运行此操作：

```PowerShell
$logpath = "C:\test\log1"
$servers = @("sa18n22-2", "sa18n22-3", "sa18n22-4")
$uri = "https://sa18n22sdn.sa18.nttest.microsoft.com"

# Create log directories on the hosts
invoke-command -Computername $servers  {
    param(
        $Path
    )
    mkdir $path    -force
} -argumentlist $LogPath

# Set firewall auditing settings on Network Controller
$AuditProperties = new-object Microsoft.Windows.NetworkController.AuditingSettingsProperties
$AuditProperties.OutputDirectory = $logpath
set-networkcontrollerauditingsettingsconfiguration -connectionuri $uri -properties $AuditProperties -force  | out-null

# Enable logging on each server
$servers = get-networkcontrollerserver -connectionuri $uri
foreach ($s in $servers) {
    $s.properties.AuditingEnabled = @("Firewall")
    new-networkcontrollerserver -connectionuri $uri -resourceid $s.resourceid -properties $s.properties -force | out-null
}
```

启用后，每台主机上的指定目录中大约一小时会出现一个新文件。  应定期处理这些文件并将它们从主机中删除。  当前文件的长度为零。该文件处于锁定状态，一直锁定到在下一个小时标记处进行刷新为止：

```syntax
PS C:\test\log1> dir

    Directory: C:\test\log1

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/19/2018   6:28 AM          17055 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL122803093.json
-a----        7/19/2018   7:28 AM           7880 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL132803173.json
-a----        7/19/2018   8:28 AM           7867 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL142803264.json
-a----        7/19/2018   9:28 AM          10949 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL152803360.json
-a----        7/19/2018   9:28 AM              0 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL162803464.json
```

这些文件包含一个流事件序列，例如：

```syntax
{
    "records": [
        {
            "properties":{
                "Version":"1.0",
                "flows":[
                    {
                        "flows":[
                            {
                                "flowTuples":["1531963580,192.122.0.22,192.122.255.255,138,138,U,I,A"],
                                "portId":"9",
                                "portName":"7290436D-0422-498A-8EB8-C6CF5115DACE"
                            }
                        ],
                        "rule":"Allow_Inbound"
                    }
                ]
            },
            "operationName":"NetworkSecurityGroupFlowEvents",
            "resourceId":"394f647d-2ed0-4c31-87c5-389b8c0c8132",
            "time":"20180719:L012620622",
            "category":"NetworkSecurityGroupFlowEvent",
            "systemId":"d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a"
            },
```

请注意，只有“日志记录”设置为“启用”的规则才会进行日志记录 ，例如：

```syntax
{
    "Tags":  null,
    "ResourceRef":  "/accessControlLists/AllowAll",
    "InstanceId":  "4a63e1a5-3264-4986-9a59-4e77a8b107fa",
    "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
    "ResourceMetadata":  null,
    "ResourceId":  "AllowAll",
    "Properties":  {
                       "ConfigurationState":  null,
                       "ProvisioningState":  "Succeeded",
                       "AclRules":  [
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Inbound",
                                            "InstanceId":  "ba8710a8-0f01-422b-9038-d1f2390645d7",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Inbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "101",
                                                               "Description":  null,
                                                               "Type":  "Inbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        },
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Outbound",
                                            "InstanceId":  "068264c6-2186-4dbc-bbe7-f504c6f47fa8",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Outbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "110",
                                                               "Description":  null,
                                                               "Type":  "Outbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        }
                                    ],
                       "IpConfigurations":  [

                                            ],
                       "Subnets":  [
                                       {
                                           "ResourceMetadata":  null,
                                           "ResourceRef":  "/virtualNetworks/10_0_1_0/subnets/Subnet1",
                                           "InstanceId":  "00000000-0000-0000-0000-000000000000",
                                           "Etag":  null,
                                           "ResourceId":  null,
                                           "Properties":  null
                                       }
                                   ]
                   }
}
```

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [数据中心防火墙概述](../concepts/datacenter-firewall-overview.md)
- [网络控制器概述](../concepts/network-controller-overview.md)
- [Azure Stack HCI 和 Windows Server 中的 SDN](../concepts/software-defined-networking.md)