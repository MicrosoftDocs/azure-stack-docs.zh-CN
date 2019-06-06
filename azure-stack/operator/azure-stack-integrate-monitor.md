---
title: 将外部监视解决方案与 Azure Stack 集成 | Microsoft Docs
description: 了解如何将 Azure Stack 与数据中心内的外部监视解决方案集成。
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
ms.openlocfilehash: e0c3c4740a1bc8073e827ff9809cf1aafa029792
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691694"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>将外部监视解决方案与 Azure Stack 集成

要在外部监视 Azure Stack 基础结构，需要监视 Azure Stack 软件、物理计算机和物理网络交换机。 上述每个监视区域都提供相应的方法来检索运行状况和警报信息：

- Azure Stack 软件提供基于 REST 的 API 来检索运行状况和警报。 软件定义的技术（如存储空间直通、存储运行状况和警报）的使用是软件监视的一部分。
- 物理计算机可以通过基板管理控制器 (BMC) 提供运行状况和警报信息。
- 物理网络设备可以通过 SNMP 协议提供运行状况和警报信息。

每个 Azure Stack 解决方案随附硬件生命周期主机。 此主机运行原始设备制造商 (OEM) 硬件供应商的物理服务器和网络设备监视软件。 请咨询 OEM 提供商，看其监视解决方案能否与数据中心现有的监视解决方案集成。

> [!IMPORTANT]
> 使用的外部监视解决方案必须无代理。 不能在 Azure Stack 组件内部安装第三方代理。

下图演示 Azure Stack 集成系统、硬件生命周期主机、外部监视解决方案与外部票证/数据收集系统之间的流量流。

![显示 Azure Stack、监视与票证解决方案之间的流量的示意图。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> 不允许直接与物理服务器进行外部监视集成，访问控制列表 (ACL) 会主动阻止这种集成。  支持直接与物理网络设备进行外部监视集成，请咨询 OEM 提供商，了解如何启用此功能。

本文介绍如何将 Azure Stack 与外部监视解决方案（例如 System Center Operations Manager 和 Nagios）集成。 此外，还介绍如何使用 PowerShell 或 REST API 调用以编程方式处理警报。

## <a name="integrate-with-operations-manager"></a>与 Operations Manager 集成

可将 Operations Manager 用于 Azure Stack 的外部监视。 Microsoft Azure Stack 的 System Center 管理包可监视多个 Azure Stack 部署与单个操作管理器实例。 该管理包使用运行状况资源提供程序和更新资源提供程序 REST API 来与 Azure Stack 通信。 如果打算绕过硬件生命周期主机上运行的 OEM 监视软件，则可以安装供应商管理包来监视物理服务器。 还可以使用 Operations Manager 网络设备发现来监视网络交换机。

适用于 Azure Stack 的管理包提供以下功能：

- 可以管理多个 Azure Stack 部署
- 支持 Azure Active Directory (Azure AD) 和 Active Directory 联合身份验证服务 (AD FS)
- 可以检索和关闭警报
- 提供运行状况和容量仪表板
- 正在修补和更新 (P&U) 时可以执行自动维护模式检测
- 包含针对部署和区域的强制更新任务
- 可将自定义信息添加到区域
- 支持通知和报告

您可以下载 System Center 管理包的 Microsoft Azure Stack 和相关联[用户指南](https://www.microsoft.com/en-us/download/details.aspx?id=55184)，或直接从 Operations Manager。

对于票证解决方案，可将 Operations Manager 与 System Center Service Manager 集成。 集成的产品连接器支持双向通信，可让你在解决 Service Manager 中的服务请求之后关闭 Azure Stack 和 Operations Manager 中的警报。

下图演示了 Azure Stack 与现有 System Center 部署的集成。 可以进一步使用 System Center Orchestrator 或 Service Management Automation (SMA) 将 Service Manager 自动化，以便在 Azure Stack 中运行操作。

![演示与 OM、Service Manager 和 SMA 集成的示意图。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>与 Nagios 集成

您可以设置和配置适用于 Microsoft Azure Stack 的 Nagios 插件。

Nagios 监视插件是与合作伙伴 Cloudbase 解决方案一起开发的，根据 MIT（麻省理工学院）的宽松免费软件许可条款提供。

该插件以 Python 编写，利用运行状况资源提供程序 REST API。 它提供在 Azure Stack 中检索和关闭警报的基本功能。 与 System Center 管理包一样，它可以让你添加多个 Azure Stack 部署以及发送通知。

使用版本 1.2 Azure Stack-Nagios 插件利用 Microsoft ADAL 库，并支持与机密或证书使用服务主体进行身份验证。 此外，配置已使用新参数的单个配置文件得到简化。 它现在支持使用 AAD 和 ADFS 作为标识系统的 Azure Stack 部署。

该插件适用与 Nagios 4 x 和 XI。 可以在[此处](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)下载。 下载站点还包含安装和配置详细信息。

### <a name="requirements-for-nagios"></a>Nagios 的要求

1.  Nagios 的最低版本为 4.x

2.  Microsoft Azure Active Directory Python 库。 此功能可以使用 Python PIP 安装。

```bash  
sudo pip install adal pyyaml six
```

### <a name="install-plugin"></a>安装插件

本部分介绍如何安装 Azure Stack 插件假设 Nagios 的默认安装。

插件包中包含以下文件：

```
  azurestack_plugin.py
  azurestack_handler.sh
  samples/etc/azurestack.cfg
  samples/etc/azurestack_commands.cfg
  samples/etc/azurestack_contacts.cfg
  samples/etc/azurestack_hosts.cfg
  samples/etc/azurestack_services.cfg
```

1.  复制该插件`azurestack_plugin.py`到以下目录`/usr/local/nagios/libexec`。

2.  复制该处理程序`azurestack_handler.sh`到以下目录`/usr/local/nagios/libexec/eventhandlers`。

3.  请设置为可执行文件的插件

    ```bash
      sudo cp azurestack_plugin.py <PLUGINS_DIR>
      sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>配置插件

可以使用 azurestack.cfg 文件中配置以下参数。 以粗体显示的参数需要配置独立于您选择的身份验证模型。

详细的信息介绍了如何创建一个 SPN[此处](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals)。

| 参数 | 描述 | Authentication |
| --- | --- | --- |
| * * External_domain_fqdn * * | 外部域 FQDN |    |
| * * 区域: * * | 区域名称 |    |
| * * tenant_id: * * | 租户 ID\* |    |
| client_id: | 客户端 ID | 使用机密的 SPN |
| client_secret: | 客户端密码 | 使用机密的 SPN |
| client_cert\*\*: | 证书的路径 | 使用证书的 SPN |
| client_cert_thumbprint\*\*: | 证书指纹 | 使用证书的 SPN |

\*租户 ID 不是使用 ADFS 的 Azure Stack 部署所必需的。

\*\* 客户端机密和客户端证书是互相排斥。

其他配置文件包含可选的配置设置，因为它们可以同时配置 Nagios 中。

> [!Note]  
> 检查在 azurestack_hosts.cfg 和 azurestack_services.cfg 位置目标。

| 配置 | 描述 |
| --- | --- |
| azurestack_commands.cfg | 处理程序配置无更改 |
| azurestack_contacts.cfg | 通知设置 |
| azurestack_hosts.cfg | Azure Stack 部署命名 |
| azurestack_services.cfg | 服务的配置 |

### <a name="setup-steps"></a>设置步骤

1.  修改配置文件

2.  将修改后的配置文件复制到以下文件夹`/usr/local/nagios/etc/objects`。

### <a name="update-nagios-configuration"></a>更新 Nagios 配置

Nagios 配置需要更新，以确保 Azure Stack – Nagios 插件加载。

1.  打开以下文件

```bash  
/usr/local/nagios/etc/nagios.cfg
```

1.  添加以下条目

```bash  
  #load the Azure Stack Plugin Configuration
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

1.  重新加载 Nagios

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>手动关闭的活动警报

可以在使用自定义通知功能的 Nagios 内关闭的活动警报。 必须为自定义通知：

```
  /close-alert <ALERT_GUID>
```

此外可以使用终端使用以下命令关闭警报：

```bash
  /usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>故障排除

故障排除的插件可能会在终端中手动调用插件来完成。 使用以下方法：

```bash
  /usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 监视运行状况和警报

如果不使用 Operations Manager、Nagios 或基于 Nagios 的解决方案，可以使用 PowerShell 来启用广泛的监视解决方案，以便与 Azure Stack 集成。

1. 若要使用 PowerShell，请确保已针对 Azure Stack 操作员环境[安装并配置 PowerShell](azure-stack-powershell-install.md)。 在可以访问资源管理器（管理员）终结点 (https://adminmanagement.[region].[External_FQDN]) 的本地计算机上安装 PowerShell。

2. 以 Azure Stack 操作员身份运行以下命令，以连接到 Azure Stack 环境：

   ```powershell
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. 使用如下所示的命令来处理警报：
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>了解详细信息

有关内置运行状况监视的信息，请参阅[在 Azure Stack 中监视运行状况和警报](azure-stack-monitor-health.md)。

## <a name="next-steps"></a>后续步骤

[安全集成](azure-stack-integrate-security.md)
