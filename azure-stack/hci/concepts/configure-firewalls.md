---
title: 为 Azure Stack HCI 配置防火墙
description: 本主题提供有关如何为 Azure Stack HCI 操作系统配置防火墙的指南。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 0bfd97b71774662ec11074951dcc956391d0fc65
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487385"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>为 Azure Stack HCI 配置防火墙

>适用于：Azure Stack HCI，版本 20H2

本主题提供有关如何为 Azure Stack HCI 操作系统配置防火墙的指南。 它包括连接要求，并说明服务标记如何在 Azure 中将操作系统需要访问的 IP 地址分组。 本主题还提供更新 Microsoft Defender 防火墙的步骤。

## <a name="connectivity-requirements"></a>连接要求
Azure Stack HCI 需要定期连接到 Azure。 访问仅限于：
- 众所周知的 Azure Ip
- 出站方向
- 端口 443 (HTTPS) 

有关详细信息，请参阅[AZURE STACK HCI 常见问题](../faq.md)中的 "Azure Stack HCI 连接性" 部分

本主题介绍如何选择使用高度锁定的防火墙配置来阻止所有目标流量（包括在允许列表中的流量除外）。

   >[!IMPORTANT]
   > 如果外部企业防火墙或代理服务器限制了出站连接，请确保不阻止下表中列出的 Url。 相关信息，请参阅 [支持 Azure Arc 的服务器代理概述](/azure/azure-arc/servers/agent-overview#networking-configuration)中的 "网络配置" 部分。


如下所示，Azure Stack HCI 可能会使用多个防火墙访问 Azure。

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="关系图显示 Azure Stack HCI 通过端口 443 (HTTPS) 防火墙访问服务标记终结点。" lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>使用服务标记
*服务标记* 代表来自给定 Azure 服务的一组 IP 地址。 Microsoft 管理服务标记中包含的 IP 地址，并在 IP 地址更改时自动更新服务标记以保持最小更新。 若要了解详细信息，请参阅 [虚拟网络服务标记](/azure/virtual-network/service-tags-overview)。

## <a name="required-endpoint-daily-access-after-azure-registration"></a>Azure 注册后 (每日所需的终结点访问权限) 
Azure 为使用服务标记组织的 Azure 服务维护众所周知的 IP 地址。 Azure 发布每个服务的所有 IP 地址的每周一个 JSON 文件。 IP 地址不会经常更改，但是每年更改几次。 下表显示了操作系统需要访问的服务标记终结点。

| 说明                   | IP 范围的服务标记  | URL                                                                                 |
| :-----------------------------| :-----------------------  | :---------------------------------------------------------------------------------- |
| Azure Active Directory        | AzureActiveDirectory      | `https://login.microsoftonline.com`<br> `https://graph.microsoft.com`               |
| Azure 资源管理器        | AzureResourceManager      | `https://management.azure.com`                        |
| Azure Stack HCI 云服务 | AzureFrontDoor.Frontend   | `https://azurestackhci.azurefd.net` |
| Azure Arc                     | AzureArcInfrastructure<br> AzureTrafficManager | 取决于您要使用的功能：<br> 混合标识服务： `*.his.arc.azure.com`<br> 来宾配置： `*.guestconfiguration.azure.com`<br> **注意：** 当我们启用更多功能时，需要更多的 Url。 |

## <a name="update-microsoft-defender-firewall"></a>更新 Microsoft Defender 防火墙
本部分说明如何将 Microsoft Defender 防火墙配置为允许与服务标记相关联的 IP 地址连接到操作系统：

1. 将以下资源的 JSON 文件下载到运行操作系统的目标计算机： [AZURE IP 范围和服务标记–公有云](https://www.microsoft.com/download/details.aspx?id=56519)。

1. 使用以下 PowerShell 命令打开 JSON 文件：

    ```powershell
    $json = Get-Content -Path .\ServiceTags_Public_20201012.json | ConvertFrom-Json
    ```

1. 获取给定服务标记的 IP 地址范围列表，如 "AzureResourceManager" 服务标记：

    ```powershell
    $IpList = ($json.values | where Name -Eq "AzureResourceManager").properties.addressPrefixes
    ```

1. 将 IP 地址列表导入到外部公司防火墙（如果你使用的是允许列表）。

1. 为群集中的每个服务器创建防火墙规则，以允许出站 443 (HTTPS) 到 IP 地址范围列表的流量：

    ```powershell
    New-NetFirewallRule -DisplayName "Allow Azure Resource Manager" -RemoteAddress $IpList -Direction Outbound -LocalPort 443 -Protocol TCP -Action Allow -Profile Any -Enabled True
    ```

## <a name="additional-endpoint-for-one-time-azure-registration"></a>用于一次性 Azure 注册的其他终结点
在 Azure 注册过程中，当你运行 `Register-AzStackHCI` 或使用 Windows 管理中心时，该 cmdlet 将尝试与 PowerShell 库联系以验证你是否具有所需的最新版本的 PowerShell 模块，如 Az 和 AzureAD。

尽管 PowerShell 库在 Azure 上托管，但目前没有服务标记。 如果无法 `Register-AzStackHCI` 从服务器节点运行该 cmdlet，原因是没有 internet 访问权限，我们建议将这些模块下载到管理计算机，然后手动将它们传输到要运行 cmdlet 的服务器节点。

## <a name="set-up-a-proxy-server"></a>设置代理服务器
若要为 Azure Stack HCI 设置代理服务器，请以管理员身份运行以下 PowerShell 命令：

```powershell
Set-WinInetProxy -ProxySettingsPerUser 0 -ProxyServer webproxy1.com:9090
```

使用 `ProxySettingsPerUser 0` 标志使代理配置服务器范围而不是每个用户（默认值）。 

在以下位置下载 WinInetProxy hbase-runner.psm1 脚本： [PowerShell 库 |WinInetProxy. hbase-runner.psm1 0.1.0](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0/Content/WinInetProxy.psm1)。

## <a name="network-port-requirements"></a>网络端口要求
请确保站点内和站点间的所有服务器节点之间的相应网络端口处于打开状态（对于延伸群集）。 你需要适当的防火墙和路由器规则，以允许在群集中的所有服务器之间进行 ICMP、SMB（端口 445，以及适用于 SMB Direct 的端口 5445）和 WS-MAN（端口 5985）双向通信。

使用 Windows Admin Center 中的群集创建向导来创建群集时，向导会针对故障转移群集、Hyper-V 和存储副本自动打开群集中每台服务器上的相应防火墙端口。 如果要在每个服务器上使用不同的防火墙，请打开以下端口：

### <a name="failover-clustering-ports"></a>故障转移群集端口
- ICMPv4 和 ICMPv6
- TCP 端口 445
- RPC 动态端口
- TCP 端口 135
- TCP 端口 137
- TCP 端口 3343
- UDP 端口 3343

### <a name="hyper-v-ports"></a>Hyper-V 端口
- TCP 端口 135
- TCP 端口 80（HTTP 连接）
- TCP 端口 443（HTTPS 连接）
- TCP 端口 6600
- TCP 端口 2179
- RPC 动态端口
- RPC 终结点映射程序
- TCP 端口 445

### <a name="storage-replica-ports-stretched-cluster"></a>存储副本端口（延伸群集）
- TCP 端口 445
- TCP 5445（如果使用 iWarp RDMA）
- TCP 端口 5985
- ICMPv4 和 ICMPv6（如果使用 `Test-SRTopology` PowerShell cmdlet）

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：
- [AZURE STACK HCI 常见问题](../faq.md)的 "连接" 部分