---
title: 将外部监视解决方案与 Azure Stack Hub 集成
description: 了解如何将 Azure Stack Hub 与数据中心内的外部监视解决方案集成。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 1a8e4618f81f157ff05117505bf5b76922bd4c35
ms.sourcegitcommit: 2d2ae0b6db2e4f43f8496b184f30cddbb08b2cbd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91815132"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack-hub"></a>将外部监视解决方案与 Azure Stack Hub 集成

要在外部监视 Azure Stack Hub 基础结构，需要监视 Azure Stack Hub 软件、物理计算机和物理网络交换机。 上述每个监视区域都提供相应的方法来检索运行状况和警报信息：

- Azure Stack Hub 软件提供基于 REST 的 API 来检索运行状况和警报。 软件定义的技术（如存储空间直通、存储运行状况和警报）的使用是软件监视的一部分。
- 物理计算机可以通过基板管理控制器 (BMC) 提供运行状况和警报信息。
- 物理网络设备可以通过 SNMP 协议提供运行状况和警报信息。

每个 Azure Stack Hub 解决方案随附硬件生命周期主机。 此主机针对物理服务器和网络设备运行原始设备制造商 (OEM) 硬件供应商的监视软件。 请咨询 OEM 提供商，看其监视解决方案能否与数据中心现有的监视解决方案集成。

> [!IMPORTANT]
> 使用的外部监视解决方案必须无代理。 不能在 Azure Stack Hub 组件内部安装第三方代理。

下图演示 Azure Stack Hub 集成系统、硬件生命周期主机、外部监视解决方案与外部票证/数据收集系统之间的流量流。

![显示 Azure Stack Hub、监视与票证解决方案之间的流量的示意图。](media/azure-stack-integrate-monitor/monitoringintegration.svg)  

> [!NOTE]
> 不允许直接与物理服务器进行外部监视集成，访问控制列表 (ACL) 会主动阻止这种集成。 支持直接与物理网络设备进行外部监视集成。 请咨询 OEM 提供商，了解如何启用此功能。

本文介绍如何将 Azure Stack Hub 与外部监视解决方案（例如 System Center Operations Manager 和 Nagios）集成。 此外，还介绍如何使用 PowerShell 或 REST API 调用以编程方式处理警报。

## <a name="integrate-with-operations-manager"></a>与 Operations Manager 集成

可将 Operations Manager 用于 Azure Stack Hub 的外部监视。 使用用于 Microsoft Azure Stack 集线器的 System Center 管理包，你可以使用单个 Operations Manager 实例来监视多个 Azure Stack 中心部署。 该管理包使用运行状况资源提供程序和更新资源提供程序 REST API 来与 Azure Stack Hub 通信。 如果打算绕过硬件生命周期主机上运行的 OEM 监视软件，则可以安装供应商管理包来监视物理服务器。 还可以使用 Operations Manager 网络设备发现来监视网络交换机。

适用于 Azure Stack Hub 的管理包提供以下功能：

- 可以管理多个 Azure Stack Hub 部署。
- 支持 Azure Active Directory (Azure AD) 和 Active Directory 联合身份验证服务 (AD FS)。
- 可以检索和关闭警报。
- 提供运行状况和容量仪表板。
- 正在修补和更新 (P&U) 时可以执行自动维护模式检测。
- 包含针对部署和区域的强制更新任务。
- 可将自定义信息添加到区域。
- 支持通知和报告。

若要下载 System Center 管理包和关联的用户指南，请参阅 [下载适用于 Microsoft Azure Stack 集线器的 System Center 管理包](https://www.microsoft.com/en-us/download/details.aspx?id=55184)。 也可直接从 Operations Manager 下载它。

对于票证解决方案，可将 Operations Manager 与 System Center Service Manager 集成。 集成的产品连接器支持双向通信，可让你在解决 Service Manager 中的服务请求之后关闭 Azure Stack Hub 和 Operations Manager 中的警报。

下图演示了 Azure Stack Hub 与现有 System Center 部署的集成。 可以进一步使用 System Center Orchestrator 或 Service Management Automation (SMA) 将 Service Manager 自动化，以便在 Azure Stack Hub 中运行操作。

![演示与 OM、Service Manager 和 SMA 集成的示意图。](media/azure-stack-integrate-monitor/systemcenterintegration.svg)

## <a name="integrate-with-nagios"></a>与 Nagios 集成

可以为 Microsoft Azure Stack 中心设置和配置 Nagios 插件。

Nagios 监视插件是与合作伙伴 Cloudbase 解决方案一起开发的，根据 MIT（麻省理工学院）的宽松免费软件许可条款提供。

该插件以 Python 编写，利用运行状况资源提供程序 REST API。 它提供在 Azure Stack Hub 中检索和关闭警报的基本功能。 与 System Center 管理包一样，它可以让你添加多个 Azure Stack Hub 部署以及发送通知。

在版本 1.2 中，Azure Stack Hub – Nagios 插件利用 Microsoft ADAL 库，并支持使用服务主体通过机密或证书进行身份验证。 此外，配置过程已通过单个配置文件与新的参数进行简化。 它现在支持使用 Azure AD 和 AD FS 作为标识系统来部署 Azure Stack Hub。

> [!IMPORTANT]
> AD FS 仅支持交互式登录会话。 如果需要对自动化场景进行非交互式登录，则必须使用 SPN。

该插件适用于 Nagios 4x 和 XI。 若要下载该插件，请参阅 [监视 Azure Stack 集线器警报](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)。 下载站点还包含安装和配置详细信息。

### <a name="requirements-for-nagios"></a>Nagios 的要求

1. Nagios 的最低版本是 4.x

2. Microsoft Azure Active Directory Python 库。 可以使用 Python PIP 安装该库。

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

1. 将插件 `azurestack_plugin.py` 复制到以下目录：`/usr/local/nagios/libexec`。

2. 将处理程序 `azurestack_handler.sh` 复制到以下目录：`/usr/local/nagios/libexec/eventhandlers`。

3. 确保将插件文件设置为可执行文件：

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>配置插件

可在 azurestack.cfg 文件中配置以下参数。 无论选择哪种身份验证模型，都需要配置以粗体显示的参数。

有关如何创建 SPN 的详细信息，请参阅[使用应用标识来访问资源](azure-stack-create-service-principals.md)。

| 参数 | 说明 | 身份验证 |
| --- | --- | --- |
| **External_domain_fqdn** | 外部域 FQDN |    |
| **区** | 区域名称 |    |
| **tenant_id：** | 租户 ID\* |    |
| client_id: | 客户端 ID | 包含机密的 SPN |
| client_secret: | 客户端密码 | 包含机密的 SPN |
| client_cert\*\*: | 证书的路径 | 包含证书的 SPN |
| client_cert_thumbprint\*\*: | 证书指纹 | 包含证书的 SPN |

\*AD FS Azure Stack 中心部署不需要租户 ID。

\*\* 客户端机密和客户端证书互斥。

其他配置文件包含可选的配置设置，也可以在 Nagios 中配置这些设置。

> [!Note]  
> 检查 azurestack_hosts.cfg 和 azurestack_services.cfg 中的位置目标。

| 配置 | 说明 |
| --- | --- |
| azurestack_commands.cfg | 处理程序配置没有更改要求 |
| azurestack_contacts.cfg | 通知设置 |
| azurestack_hosts.cfg | Azure Stack 中心部署命名 |
| azurestack_services.cfg | 服务的配置 |

### <a name="setup-steps"></a>设置步骤

1. 修改配置文件。

2. 将修改后的配置文件复制到以下文件夹：`/usr/local/nagios/etc/objects`。

### <a name="update-nagios-configuration"></a>更新 Nagios 配置

需要更新 Nagios 配置，以确保加载 Azure Stack 集线器– Nagios 插件。

1. 打开以下文件：

   ```bash  
   /usr/local/nagios/etc/nagios.cfg
   ```

2. 添加以下条目：

   ```bash  
   # Load the Azure Stack Hub Plugin Configuration
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
   ```

3. 重新加载 Nagios。

   ```bash  
   sudo service nagios reload
   ```

### <a name="manually-close-active-alerts"></a>手动关闭活动的警报

可以使用自定义通知功能在 Nagios 内部关闭活动的警报。 自定义通知必须是：

```
/close-alert <ALERT_GUID>
```

还可以运行以下命令使用终端关闭警报：

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>疑难解答

通过在终端中手动调用插件，对插件进行故障排除。 使用以下方法：

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 监视运行状况和警报

如果使用的不是 Operations Manager、Nagios 或基于 Nagios 的解决方案，则可以使用 PowerShell 来启用范围广泛的监视解决方案，以便与 Azure Stack 中心集成。

1. 若要使用 PowerShell，请确保已为 Azure Stack 中心运营商环境 [安装并配置 powershell](azure-stack-powershell-install.md) 。 在可以访问资源管理器（管理员）终结点 (https://adminmanagement.[region].[External_FQDN]) 的本地计算机上安装 PowerShell。

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

有关内置运行状况监视的信息，请参阅 [监视 Azure Stack 集线器中的运行状况和警报](azure-stack-monitor-health.md)。

## <a name="next-steps"></a>后续步骤

[安全集成](azure-stack-integrate-security.md)
