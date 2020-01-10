---
title: 将外部监视解决方案与 Azure Stack 中心集成 |Microsoft Docs
description: 了解如何在你的数据中心中将 Azure Stack 集线器与外部监视解决方案集成。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: e9e66908cd42a72629caa12163c0c56790cae82f
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811356"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack-hub"></a>将外部监视解决方案与 Azure Stack 中心集成

对于 Azure Stack 中心基础结构的外部监视，需要监视 Azure Stack 中心软件、物理计算机和物理网络交换机。 其中每个区域都提供了一种方法来检索运行状况和警报信息：

- Azure Stack 集线器软件提供基于 REST 的 API 来检索运行状况和警报。 使用软件定义的技术（例如存储空间直通、存储运行状况和警报）是软件监视的一部分。
- 物理计算机可以通过基板管理控制器（Bmc）提供运行状况和警报信息。
- 物理网络设备可以通过 SNMP 协议提供运行状况和警报信息。

每个 Azure Stack 集线器解决方案随硬件生命周期主机一起提供。 此主机对物理服务器和网络设备运行原始设备制造商（OEM）硬件供应商的监视软件。 如果其监视解决方案可与数据中心内现有的监视解决方案集成，请与 OEM 提供商联系。

> [!IMPORTANT]
> 你使用的外部监视解决方案必须无代理。 无法在 Azure Stack Hub 组件内安装第三方代理。

下图显示了 Azure Stack 集线器集成系统、硬件生命周期主机、外部监视解决方案和外部票证/数据收集系统之间的通信流。

![显示 Azure Stack 集线器、监视和票证发放解决方案之间的流量的关系图。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> 外部监视与物理服务器直接集成不允许访问控制列表（Acl），并且主动阻止这些集成。 支持直接与物理网络设备集成的外部监视。 请咨询 OEM 提供商，了解如何启用此功能。

本文介绍如何将 Azure Stack 中心与外部监视解决方案（例如 System Center Operations Manager 和 Nagios）集成。 它还包括如何使用 PowerShell 或通过 REST API 调用以编程方式处理警报。

## <a name="integrate-with-operations-manager"></a>与 Operations Manager 集成

您可以使用 Operations Manager 来监视 Azure Stack 中心的外部监视。 使用用于 Microsoft Azure Stack 集线器的 System Center 管理包，你可以使用单个 Operations Manager 实例来监视多个 Azure Stack 中心部署。 管理包使用运行状况资源提供程序和更新资源提供程序 REST Api 与 Azure Stack 中心通信。 如果打算绕过在硬件生命周期主机上运行的 OEM 监视软件，可以安装供应商管理包来监视物理服务器。 你还可以使用 Operations Manager 网络设备发现来监视网络交换机。

Azure Stack 中心的管理包提供了以下功能：

- 可以管理多个 Azure Stack 中心部署。
- 支持 Azure Active Directory （Azure AD）和 Active Directory 联合身份验证服务（AD FS）。
- 可以检索和关闭警报。
- 有一个运行状况和容量仪表板。
- 包含修补和更新（P & U）正在进行时的自动维护模式检测。
- 包括针对部署和区域的强制更新任务。
- 您可以将自定义信息添加到区域。
- 支持通知和报告。

若要下载 System Center 管理包和关联的用户指南，请参阅[下载适用于 Microsoft Azure Stack 集线器的 System Center 管理包](https://www.microsoft.com/en-us/download/details.aspx?id=55184)。 还可以直接从 Operations Manager 下载。

对于票证解决方案，可以将 Operations Manager 与 System Center Service Manager 集成。 集成产品连接器允许双向通信，使你可以在 Service Manager 中解决服务请求后，关闭 Azure Stack 集线器和 Operations Manager 中的警报。

下图显示了 Azure Stack 中心与现有 System Center 部署的集成。 可以通过 System Center Orchestrator 或 Service Management Automation （SMA）来自动执行 Service Manager，以便在 Azure Stack Hub 中运行操作。

![显示与 OM、Service Manager 和 SMA 集成的关系图。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>与 Nagios 集成

可以为 Microsoft Azure Stack 中心设置和配置 Nagios 插件。

Nagios 监视插件是随合作伙伴 Cloudbase 解决方案一起开发的，后者可在免费的软件许可证-MIT （马萨诸塞州协会）下获得。

此插件是用 Python 编写的，并利用了运行状况资源提供程序 REST API。 它提供了用于在 Azure Stack 中心检索和关闭警报的基本功能。 与 System Center 管理包类似，它使你可以添加多个 Azure Stack 中心部署并发送通知。

对于版本1.2，Azure Stack 集线器– Nagios 插件利用 Microsoft ADAL 库，并支持使用包含机密或证书的服务主体进行身份验证。 此外，配置已使用具有新参数的单个配置文件进行了简化。 它现在支持使用 Azure AD 和 AD FS 作为标识系统的 Azure Stack 中心部署。

该插件适用于 Nagios 4x 和 XI。 若要下载该插件，请参阅[监视 Azure Stack 集线器警报](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)。 下载站点还包括安装和配置详细信息。

### <a name="requirements-for-nagios"></a>Nagios 的要求

1. 最小 Nagios 版本为4。x

2. Microsoft Azure Active Directory Python 库。 此库可使用 Python PIP 进行安装。

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>安装插件

本部分介绍如何安装 Azure Stack 集线器插件，前提是 Nagios 的默认安装。

插件包包含以下文件：

```
azurestack_plugin.py
azurestack_handler.sh
samples/etc/azurestack.cfg
samples/etc/azurestack_commands.cfg
samples/etc/azurestack_contacts.cfg
samples/etc/azurestack_hosts.cfg
samples/etc/azurestack_services.cfg
```

1. 将插件 `azurestack_plugin.py` 复制到以下目录中： "`/usr/local/nagios/libexec`"。

2. 将处理程序 `azurestack_handler.sh` 复制到以下目录中： `/usr/local/nagios/libexec/eventhandlers`。

3. 请确保将插件文件设置为可执行文件：

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>配置插件

可在 test-azurestack 文件中配置以下参数。 粗体的参数需要独立于所选的身份验证模型进行配置。

有关如何创建 SPN 的详细信息，请参阅[使用应用标识来访问资源](azure-stack-create-service-principals.md)。

| 参数 | Description | 身份验证 |
| --- | --- | --- |
| \* * External_domain_fqdn * * | 外部域 FQDN |    |
| \* * region： * * | 区域名称 |    |
| \* * tenant_id： * * | 租户 ID\* |    |
| client_id： | 客户端 ID | 带有机密的 SPN |
| client_secret： | 客户端密码 | 带有机密的 SPN |
| client_cert\*\*： | 证书的路径 | 带有证书的 SPN |
| client_cert_thumbprint\*\*： | 证书指纹 | 带有证书的 SPN |

具有 AD FS 的 Azure Stack 中心部署不需要 \*租户 ID。

\*\* 客户端密码和客户端证书互相排斥。

其他配置文件包含可在 Nagios 中配置的可选配置设置。

> [!Note]  
> 检查 location 目标 azurestack_hosts cfg 和 azurestack_services。

| 配置 | Description |
| --- | --- |
| azurestack_commands.cfg | 处理程序配置无更改要求 |
| azurestack_contacts.cfg | 通知设置 |
| azurestack_hosts.cfg | Azure Stack 中心部署命名 |
| azurestack_services.cfg | 服务的配置 |

### <a name="setup-steps"></a>设置步骤

1. 修改配置文件。

2. 将修改后的配置文件复制到以下文件夹： `/usr/local/nagios/etc/objects`。

### <a name="update-nagios-configuration"></a>更新 Nagios 配置

需要更新 Nagios 配置，以确保加载 Azure Stack 集线器– Nagios 插件。

1. 打开以下文件：

   ```bash  
   /usr/local/nagios/etc/nagios.cfg
   ```

2. 添加以下项：

   ```bash  
   # Load the Azure Stack Hub Plugin Configuration
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
   ```

3. 重载 Nagios。

   ```bash  
   sudo service nagios reload
   ```

### <a name="manually-close-active-alerts"></a>手动关闭活动警报

可使用自定义通知功能在 Nagios 中关闭活动警报。 自定义通知必须：

```
/close-alert <ALERT_GUID>
```

还可以通过以下命令使用终端关闭警报：

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>故障排除

排查插件问题时，可通过在终端中手动调用插件来完成此操作。 使用以下方法：

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 监视运行状况和警报

如果使用的不是 Operations Manager、Nagios 或基于 Nagios 的解决方案，则可以使用 PowerShell 来启用范围广泛的监视解决方案，以便与 Azure Stack 中心集成。

1. 若要使用 PowerShell，请确保已为 Azure Stack 中心运营商环境[安装并配置 powershell](azure-stack-powershell-install.md) 。 在可以访问资源管理器（管理员）终结点（ https://adminmanagement. [region]。[External_FQDN]）。

2. 运行以下命令以 Azure Stack 中心操作员身份连接到 Azure Stack 集线器环境：

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. 使用如下所示的命令来处理警报：
   ```powershell
    # Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    # Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    # Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    # Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>了解更多

有关内置运行状况监视的信息，请参阅[监视 Azure Stack 集线器中的运行状况和警报](azure-stack-monitor-health.md)。

## <a name="next-steps"></a>后续步骤

[安全集成](azure-stack-integrate-security.md)
