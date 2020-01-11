---
title: 使用 Azure Stack 集线器验证工具验证系统状态 |Microsoft Docs
description: 了解如何使用 Azure Stack 集线器验证工具来验证系统状态。
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 778f38f0ed3d1b1801b162624daa365ed6d1f09c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882498"
---
# <a name="validate-azure-stack-hub-system-state"></a>验证 Azure Stack 集线器系统状态

作为 Azure Stack 中心运营商，能够根据需要确定系统的运行状况和状态是至关重要的。 Azure Stack 集线器验证工具（**test-azurestack**）是一种 PowerShell cmdlet，可让你在系统上运行一系列测试来识别故障（如果存在）。 当你联系 Microsoft 客户服务支持（CSS）问题时，通常会要求你通过[特权终结点（PEP）](azure-stack-privileged-endpoint.md)来运行此工具。 使用目前系统范围内的运行状况和状态信息，CSS 可以收集和分析详细日志，重点关注错误发生的区域，并与你一起解决问题。

## <a name="running-the-validation-tool-and-accessing-results"></a>运行验证工具并访问结果

如上所述，验证工具是通过 PEP 运行的。 每个测试都将在 PowerShell 窗口中返回 "**通过/失败**" 状态。 下面是端到端验证测试过程的概述：

1. 建立信任关系。 在集成系统上，从提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化 VM 上的受信任主机。

   ```powershell
   winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
   ```

   如果运行的是 Azure Stack 开发工具包（ASDK），请登录到开发工具包主机。

1. 访问 PEP。 运行以下命令以建立 PEP 会话：

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > 若要访问 Azure Stack 开发工具包（ASDK）主机计算机上的 PEP，请使用 AzS-ERCS01 for-ComputerName。

1. 进入 PEP 后，运行以下内容：

   ```powershell
   Test-AzureStack
   ```

   有关详细信息，请参阅[参数注意事项](azure-stack-diagnostic-test.md#parameter-considerations)和[用例示例](azure-stack-diagnostic-test.md#use-case-examples)。

1. 如果任何测试报表**失败**，请运行 `Get-AzureStackLog`。 有关集成系统的说明，请参阅[若要在 Azure Stack 集线器集成系统上](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)或在 ASDK 上运行 get-azurestacklog，请参阅在[ASDK 系统上运行 get-azurestacklog](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system)。

   Cmdlet 将收集由 Test-azurestack 生成的日志。 如果测试报告**警告**，我们建议您不要收集日志并联系 CSS。

1. 如果指示你通过 CSS 运行验证工具，则 CSS 代表会请求你收集的日志以继续排查你的问题。

## <a name="tests-available"></a>可用测试

使用验证工具可运行一系列系统级测试和基本的云方案，使您可以洞察当前状态，使您能够解决系统中的问题。

### <a name="cloud-infrastructure-tests"></a>云基础结构测试

这些低影响测试适用于基础结构级别，并提供有关各种系统组件和功能的信息。 目前，测试分为以下几类：

| 测试类别                                        | -Include 和-Ignore 的参数 |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack 中心 ACS 摘要                              | AzsAcsSummary                     |
| Azure Stack 中心 Active Directory 摘要                 | AzsAdSummary                      |
| Azure Stack 中心警报摘要                            | AzsAlertSummary                   |
| Azure Stack 中心应用程序故障摘要                | AzsApplicationCrashSummary        |
| Azure Stack 中心备份共享辅助功能摘要       | AzsBackupShareAccessibility       |
| Azure Stack 中心 BMC 摘要                              | AzsStampBMCSummary                |
| Azure Stack 中心云托管基础结构摘要     | AzsHostingInfraSummary            |
| Azure Stack 中心云托管基础结构利用率 | AzsHostingInfraUtilization        |
| Azure Stack 集线器控制平面摘要                    | AzsControlPlane                   |
| Azure Stack 中心 Defender 摘要                         | AzsDefenderSummary                |
| Azure Stack 中心托管基础结构固件摘要  | AzsHostingInfraFWSummary          |
| Azure Stack 集线器基础结构容量                  | AzsInfraCapacity                  |
| Azure Stack 中心基础结构性能               | AzsInfraPerformance               |
| Azure Stack 中心基础结构角色摘要              | AzsInfraRoleSummary               |
| Azure Stack 集线器网络基础                            | AzsNetworkInfra                   |
| Azure Stack 中心门户和 API 摘要                   | AzsPortalAPISummary               |
| Azure Stack 中心规模单元 VM 事件                     | AzsScaleUnitEvents                |
| Azure Stack 集线器规模单元 VM 资源                  | AzsScaleUnitResources             |
| Azure Stack 中心方案                                | AzsScenarios                      |
| Azure Stack 中心 SDN 验证摘要                   | AzsSDNValidation                  |
| Azure Stack 中心 Service Fabric 角色摘要              | AzsSFRoleSummary                  |
| Azure Stack 中心存储数据平面                       | AzsStorageDataPlane               |
| Azure Stack 中心存储服务摘要                 | AzsStorageSvcsSummary             |
| Azure Stack 中心 SQL 存储摘要                        | AzsStoreSummary                   |
| Azure Stack 中心更新摘要                           | AzsInfraUpdateSummary             |
| Azure Stack 中心 VM 放置摘要                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>云方案测试

除了上述基础结构测试，你还可以运行云方案测试来检查基础结构组件的功能。 需要云管理员凭据才能运行这些测试，因为它们涉及到资源部署。

> [!NOTE]
> 目前不能使用 Active Directory 联合服务（AD FS）凭据运行云方案测试。

验证工具对以下云方案进行了测试：
- 创建资源组
- 计划创建
- 产品/服务创建
- 创建存储帐户
- 虚拟机创建（VM）
- Blob 存储操作
- 队列存储操作
- 表存储操作

## <a name="parameter-considerations"></a>参数注意事项

- 参数**列表**可用于显示所有可用的测试类别。

- 可以使用 "参数**包括**" 和 "**忽略**" 来包含或排除测试类别。 有关这些参数的详细信息，请参阅下一节。

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- 租户 VM 作为云方案测试的一部分进行部署。 你可以使用**DoNotDeployTenantVm**来禁用此 VM 部署。

- 需要提供**ServiceAdminCredential**参数来运行云方案测试，如[使用案例示例](azure-stack-diagnostic-test.md#use-case-examples)部分中所述。

- 在测试基础结构备份设置时，将使用**BackupSharePath**和**BackupShareCredential** ，如[用例示例](azure-stack-diagnostic-test.md#use-case-examples)部分中所示。

- **DetailedResults**可用于获取每个测试以及整体运行的通过/失败/警告信息。 如果未指定，则**test-azurestack**会在没有失败时返回 **$true** ，如果出现故障，则返回 **$false** 。
- **TimeoutSeconds**可用于设置每个组完成的特定时间。

- 验证工具还支持常见的 PowerShell 参数： Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer、PipelineVariable 和 OutVariable。 有关详细信息，请参阅[关于通用参数](https://go.microsoft.com/fwlink/?LinkID=113216)。  

## <a name="use-case-examples"></a>用例示例

### <a name="run-validation-without-cloud-scenarios"></a>无云方案运行验证

运行不带**ServiceAdminCredential**参数的验证工具，跳过正在运行的云方案测试： 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>运行云方案验证

使用**ServiceAdminCredentials**参数提供验证工具时，默认运行云方案测试： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

如果只想运行云方案，而不运行其余测试，可以使用**Include**参数来执行此操作： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

云管理员用户名必须以 UPN 格式键入： serviceadmin@contoso.onmicrosoft.com （Azure AD）。 出现提示时，键入云管理员帐户的密码。

### <a name="groups"></a>组

为了改进操作员体验，已启用一个**组**参数来同时运行多个测试类别。 目前，定义了三个组： **Default**、 **UpdateReadiness**和**SecretRotationReadiness**。

- **默认值**： **test-azurestack**为标准运行。 如果未选择其他组，则默认情况下将运行此组。
- **UpdateReadiness**：检查是否可以更新 Azure Stack 中心实例。 运行**UpdateReadiness**组时，警告在控制台输出中显示为错误，并应将其视为更新的阻止程序。 从 Azure Stack 中心版本1910，以下类别是**UpdateReadiness**组的一部分：

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness**：查看 Azure Stack 集线器实例是否处于可运行机密旋转的状态。 运行**SecretRotationReadiness**组时，警告在控制台输出中显示为错误，应将其视为用于机密旋转的阻止程序。 以下类别是 SecretRotationReadiness 组的一部分：

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>组参数示例

下面的示例运行**test-azurestack**来测试系统准备情况，然后再使用**组**安装更新或修补程序。 在开始安装更新或修补程序之前，请运行**test-azurestack**以检查 Azure Stack 中心的状态：

```powershell
Test-AzureStack -Group UpdateReadiness
```

如果 Azure Stack 集线器运行的版本早于1811，请使用以下 PowerShell 命令运行**test-azurestack**：

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>运行验证工具以测试基础结构备份设置

在配置基础结构备份*之前*，可以使用**AzsBackupShareAccessibility**测试来测试备份共享路径和凭据：

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

配置备份*后*，你可以运行**AzsBackupShareAccessibility**来验证是否可以从 ERCS 访问共享：

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

若要通过配置的备份共享来测试新凭据，请运行： 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>运行验证工具以测试网络基础结构

此测试检查网络基础结构的连接，绕过 Azure Stack 中心软件定义的网络（SDN）。 它演示从公共 VIP 连接到已配置的 DNS 转发器、NTP 服务器和身份验证终结点。 这包括在 AD FS 使用作为标识提供者的 Azure AD 作为标识提供者或联合服务器时，与 Azure 的连接。

包含 debug 参数以获取命令的详细输出：

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Stack 集线器诊断工具和问题日志记录，请参阅[Azure Stack 集线器诊断工具](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)。

若要了解有关故障排除的详细信息，请参阅[Microsoft Azure Stack 集线器故障排除](azure-stack-troubleshooting.md)。
