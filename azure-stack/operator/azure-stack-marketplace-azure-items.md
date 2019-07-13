---
title: 适用于 Azure Stack 的 Azure 市场项 | Microsoft Docs
description: 这些 Azure 市场项可以用在 Azure Stack 中。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 07/12/2019
ms.openlocfilehash: 38427f55f07c4982b909c153a500340b6e61efde
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67851845"
---
# <a name="azure-marketplace-items-available-for-azure-stack"></a>适用于 Azure Stack 的 Azure 市场项

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包。*

## <a name="virtual-machine-extensions"></a>虚拟机扩展

只要有所用虚拟机 (VM) 扩展的更新，就应下载它们。 随产品一起提供的扩展不会在普通的修补和更新过程中更新，因此请经常查看更新。 其他扩展只能通过市场管理获取。

|  | 项名称 | 描述 | 发布者 | OS 类型 |
| --- | --- | --- | --- | --- |
|![SQL IaaS 扩展](media/azure-stack-marketplace-azure-items/cse.png) | [SQL IaaS 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| **下载此扩展以部署任何“Windows 上的 SQL Server”市场项 - 此扩展是必需的。** | Microsoft | Windows |
|![自定义脚本扩展](media/azure-stack-marketplace-azure-items/cse.png) | [自定义脚本扩展](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript)| **请下载此更新，此更新针对用于 Windows 的自定义脚本扩展的内置版本。** | Microsoft | Windows |
|![PowerShell DSC 扩展](media/azure-stack-marketplace-azure-items/dsc.png) | [PowerShell DSC 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| **请将此更新下载到 PowerShell DSC 扩展的内置版本。更新为支持 TLS v1.2。** | Microsoft | Windows |
| ![Microsoft 反恶意软件扩展](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft 反恶意软件扩展](https://docs.microsoft.com/azure/security/azure-security-antimalware)| 适用于 Azure 的 Microsoft 反恶意软件是为应用程序和租户环境，可在无需人工操作在后台运行的单一代理解决方案。 **请将此更新下载到 Antimalware 扩展的内置版本。** | Microsoft | Windows |
| ![Microsoft Azure 诊断扩展](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft Azure 诊断扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-windows)| Microsoft Azure 诊断是 Azure 中可启用对已部署的应用程序的诊断数据收集功能。 **请下载此更新，此更新针对用于 Windows 的诊断扩展的内置版本。** | Microsoft | Windows |
| ![Azure Monitor、 更新和配置管理扩展](media/azure-stack-marketplace-azure-items/cse.png) | [Azure Monitor、 更新和配置管理扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)| Azure Monitor、 更新和配置管理扩展用于与 Log Analytics、 Azure 安全中心和 Azure Sentinel 提供监视功能的虚拟机。 **请下载此更新，此更新针对用于 Windows 的 Monitoring Agent 扩展的内置版本。** | Microsoft | Windows |
|![自定义脚本扩展](media/azure-stack-marketplace-azure-items/cse.png) | - [自定义脚本扩展（版本 1，已弃用）](https://docs.microsoft.com/azure/virtual-machines/extensions/custom-script-linuxostc)</b> - [自定义脚本扩展（版本 2）](https://docs.microsoft.com/azure/virtual-machines/extensions/custom-script-linux) |**请将此更新下载到 Linux 的自定义脚本扩展的内置版本。此扩展有多个版本，应下载 1.5.2.1 和 2.0.x。** | Microsoft | Linux |
| ![适用于 Linux 的 VM 访问权限](media/azure-stack-marketplace-azure-items/cse.png) | [适用于 Linux 的 VM 访问权限](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| **请下载此更新，此更新针对适用于 Linux 的 VM 访问权限扩展的内置版本。如果计划使用 Debian Linux VM，此更新很重要。** | Microsoft | Linux |
| ![适用于 Linux 的 Acronis 备份扩展](media/azure-stack-marketplace-azure-items/acronis.png) | [适用于 Linux 的 Acronis 备份扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | 适用于 Microsoft Azure 的 Acronis 备份扩展是数据保护产品的 Acronis 备份系列的一部分。 | Acronis International GmbH。 | Linux |
| ![适用于 Windows 的 Acronis 备份扩展](media/azure-stack-marketplace-azure-items/acronis.png) | [适用于 Windows 的 Acronis 备份扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | 适用于 Microsoft Azure 的 Acronis 备份扩展是数据保护产品的 Acronis 备份系列的一部分。 | Acronis International GmbH。 | Windows |
| ![适用于 Linux 的 CloudLink SecureVM 扩展](media/azure-stack-marketplace-azure-items/cloudlink.png) | [适用于 Linux 的 CloudLink SecureVM 扩展](https://azuremarketplace.microsoft.com/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 VM。 | Dell EMC | Linux |
| ![适用于 Windows 的 CloudLink SecureVM 扩展](media/azure-stack-marketplace-azure-items/cloudlink.png) | [适用于 Windows 的 CloudLink SecureVM 扩展](https://azuremarketplace.microsoft.com/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 VM。 | Dell EMC | Windows |
| ![Kaspersky](media/azure-stack-marketplace-azure-items/kaspersky.png) | [Kaspersky 混合云安全代理中为 Windows 的](https://azuremarketplace.microsoft.com/marketplace/apps/KasperskyLab.ksws-windows-arm?tab=Overview) | 具有 Kaspersky 混合云安全性，你可以通过 Azure 扩展云工作负荷中立即设置网络安全功能。 | Kaspersky 实验室 | Windows |
| ![Kaspersky](media/azure-stack-marketplace-azure-items/kaspersky.png) | [适用于 Linux 的 Kaspersky 混合云安全代理](https://azuremarketplace.microsoft.com/marketplace/apps/KasperskyLab.kesl-linux-arm?tab=Overview) | 具有 Kaspersky 混合云安全性，你可以通过 Azure 扩展云工作负荷中立即设置网络安全功能。 | Kaspersky 实验室 | Linux |

## <a name="microsoft-virtual-machine-images-and-solution-templates"></a>Microsoft 虚拟机映像和解决方案模板

Microsoft Azure Stack 支持以下 Azure Marketplace 虚拟机和解决方案模板。 请根据说明单独下载任何依赖项。 SQL Server 和 Machine Learning Server 之类的应用程序需要适当的许可，除非已标记为“免费”或“试用”。

|  | 项名称 | 描述 | 发布者 |
| --- | --- | --- | --- |
| ![Windows Server](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer) | 企业级解决方案，部署简单，经济高效，以应用程序和用户为中心。 这些映像会定期更新，发布最新修补程序。 **重要信息：必须删除 2018 年 1 月 18 日之前下载的映像并将其替换为最新版本。** | Microsoft |
| ![SharePoint Server 2013 试用版](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 试用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2013Trial) | Microsoft SharePoint Server 2013 试用版 Windows Server 2012 Datacenter 和 Visual Studio 2019 社区版上。 | Microsoft |
| ![SharePoint Server 2016 试用版](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 试用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Windows Server 2016 Datacenter 上的 Microsoft SharePoint Server 2016 试用版。 | Microsoft |
| ![Windows Server 2012 R2 上的 SQL Server 2014 SP2](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2012 R2 上的 SQL Server 2014 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp2-ws2012r2) | SQL Server 2014 Service Pack 2。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP2 Standard](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP2 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | 适用于任务关键型智能应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 Developer](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | SQL Server 2016 SP2 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 Express](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | SQL Server 2016 SP2 的免费 Express 版本。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP2 Enterprise](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP2 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | 适用于任务关键型智能应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP2 Web](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP2 Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | 适用于任务关键型智能应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2017 Standard](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | 适用于任务关键型智能应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2017 Developer](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | SQL Server 2017 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2017 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | SQL Server 2017 的免费 Express 版本。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2017 Enterprise](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | 适用于任务关键型智能应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2017 Web](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/cognosys.sql-server-2007-web-win2016-debug-utilities) | 适用于任务关键型智能应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![Ubuntu Server 16.04 LTS 上的 SQL Server 2017](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ubuntu1604) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + Canonical |
| ![SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Standard](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonSLES12SP2?tab=Overview) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + SUSE |
| ![免费许可证：SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Developer](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2?tab=Overview) | SQL Server 2017 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。 | Microsoft + SUSE |
| ![免费许可证：SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | SQL Server 2017 的免费 Express 版本。 | Microsoft + SUSE |
| ![SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Enterprise](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseonSLES12SP2?tab=Overview) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + SUSE |
| ![SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Web](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonSLES12SP2) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + SUSE |
| ![Windows Server 2016 上的 Microsoft Machine Learning Server 9.3.0](media/azure-stack-marketplace-azure-items/microsoft.png) | [Windows Server 2016 上的 Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onWindowsServer2016) | Windows Server 2016 上的 Microsoft Machine Learning Server 9.3.0。 | Microsoft |
| ![Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3.0](media/azure-stack-marketplace-azure-items/microsoft.png) | [Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onUbuntu1604) | Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3.0。 | Microsoft + Canonical |
| ![CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3.0](media/azure-stack-marketplace-azure-items/microsoft.png) | [CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onCentOSLinux72) | CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3.0。 | Microsoft + Rogue Wave |

## <a name="linux-distributions"></a>Linux 发行版

|  | 项名称 | 描述 | 发布者 |
| --- | --- | --- | --- |
| ![Clear Linux OS](media/azure-stack-marketplace-azure-items/clearlinux.png) | [Clear Linux OS](https://azuremarketplace.microsoft.com/marketplace/apps/clear-linux-project.clear-linux-os) | 一个针对 Intel 体系结构优化的参考性 Linux 发行版 | Clear Linux Project |
| ![CoreOS 提供的 Container Linux](media/azure-stack-marketplace-azure-items/coreos.png) | [CoreOS 提供的 Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | Container Linux 是一种新式的最小型 Linux 发行版，可以方便地运行容器、管理群集以及无缝地更新服务器 - 所有组件都启用了仓库规模的计算。 | CoreOS |
| ![Ubuntu Server](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server 是全球流行的 Linux 云环境。 | Canonical |
| ![Debian 8 "Jessie"](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8“Jessie”](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux 是最流行的 Linux 分发版之一。 | credativ |
| ![基于 CentOS 的 6.8](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 6.8](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased68HPC) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic）  |
| ![基于 CentOS 的 6.10](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 6.10](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased610) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic）  |
| ![基于 CentOS 的 7.3](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 7.3](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic） |
| ![基于 CentOS 的 7.5](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 7.5](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased75) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic） |
| ![基于 CentOS 的 7.5-LVM](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 7.5-LVM](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased75) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic） |
| ![SLES 11 SP4 (BYOS)](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 11 SP4 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/suse.sles-byos) | SUSE Linux Enterprise Server 11 SP4。 | SUSE |
| ![SLES 12 SP3 (BYOS)](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP4 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/suse.sles-byos) | SUSE Linux Enterprise Server 12 SP4。 | SUSE |
| ![SLES 15 (BYOS)](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 15 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/suse.sles-byos) | SUSE Linux Enterprise Server 15。 | SUSE |

## <a name="third-party-byol-free-trial-images-and-solution-templates"></a>第三方 BYOL 版、免费版和试用版映像以及解决方案模板

|  | 项名称 | 描述 | 发布者 |
| --- | --- | --- | --- |
| ![A10 vThunder ADC](media/azure-stack-marketplace-azure-items/a10.png) | [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1) | A10 网络 vThunder 适用于 Microsoft Azure 应用程序传送控制器专为高性能、 灵活性，并轻松部署应用程序交付和服务器负载平衡和优化，可在 Azure 云中以本机方式运行。 | A10 Networks |
| ![Arista vEOS 路由器](media/azure-stack-marketplace-azure-items/arista.png) | [Arista vEOS 路由器](https://azuremarketplace.microsoft.com/marketplace/apps/arista-networks.veos-router) | Arista vEOS 路由器是功能丰富的多云，并使企业和云提供商生成一致的、 高度安全的多虚拟机监控程序虚拟路由器和可扩展的混合网络。 | Arista Networks |
| ![Barracuda Application Security Control Center](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Application Security Control Center](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-app-sec-control-center) | 集中管理多个 Barracuda Web 应用程序防火墙 (WAF)。 | Barracuda Networks, Inc. |
| ![Barracuda Email Security Gateway](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Email Security Gateway](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-email-security-gateway) | 一种电子邮件安全网关，用于防范入站电子邮件产生的威胁。 | Barracuda Networks, Inc. |
| ![Barracuda Web 应用程序防火墙 (WAF)](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Web 应用程序防火墙 (WAF)](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf) | 一种安全性和 DDoS 防护，防范自动化针对性攻击。 | Barracuda Networks, Inc. |
| ![Barracuda CloudGen 防火墙控制中心](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda CloudGen 防火墙控制中心](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-ng-cc) | 集中管理数百个 Barracuda CloudGen 防火墙而不考虑其位置和外形规格。 | Barracuda Networks, Inc. |
| ![适用于 Azure 的 barracuda CloudGen 防火墙](media/azure-stack-marketplace-azure-items/barracuda.png) | [适用于 Azure 的 barracuda CloudGen 防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-ng-firewall) | 提供应用程序和数据所在的位置，而不是在连接终止处仅防火墙保护。 | Barracuda Networks, Inc. |
| ![AbanteCart](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | 开源电子商务购物车。 | Bitnami |
| ![ActiveMQ](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | Java 中的开源消息代理。 | Bitnami |
| ![Akeneo](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 强大的 PIM，旨在简化管理过程。 | Bitnami |
| ![Alfresco Community](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | ECM 系统，方便文档管理。 | Bitnami |
| ![Apache Solr](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 可靠的开源企业搜索平台。 | Bitnami |
| ![Canvas LMS](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | 开源学习管理系统。 | Bitnami |
| ![Cassandra](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 可用性高且可缩放的开源数据库。 | Bitnami |
| ![Cassandra 群集](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra-cluster) | Apache Cassandra 是一种开源分布式数据库管理系统，旨在处理多个商品服务器的大量数据 ，可确保高可用性，不会造成单点故障。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![CiviCRM](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | 简单的基于 Web 的关系管理系统。 | Bitnami |
| ![CMS Made Simple](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [CMS Made Simple](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | 快速轻松地创建和管理网站。 | Bitnami |
| ![Concrete5](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | 轻松部署 Web 应用程序、网站、存储和论坛。 | Bitnami |
| ![Coppermine](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 完备的多用途 Web 库。 | Bitnami |
| ![CouchDB](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 易用的开源数据库系统。 | Bitnami |
| ![Diaspora](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | 常用的个人 Web 服务器。 | Bitnami |
| ![Discourse](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 高分辨率开源讨论平台。 | Bitnami |
| ![Django](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 高级 Python Web 框架。 | Bitnami |
| ![Dolibarr](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 免费的开源软件包。 | Bitnami |
| ![DokuWiki](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 通用开源 Wiki 软件。 | Bitnami |
| ![DreamFactory](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | 开源 REST API，使用 SQL、NoSQL、BLOB 之类的服务。 | Bitnami |
| ![Elasticsearch](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 灵活且功能强大的开源分析引擎。 | Bitnami |
| ![ELK](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | 由 Elasticsearch、Kibana、Logstash 组成的大数据套件。 | Bitnami |
| ![ERPNext](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | 开源企业资源规划 (ERP) 平台。 | Bitnami |
| ![EspoCRM](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 简单的 CRM 系统，可帮助管理客户关系。 | Bitnami |
| ![eXo Platform](media/azure-stack-marketplace-azure-items/exoplatform.png) | [eXo Platform](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 专为企业设计的开源社交软件。 | Bitnami |
| ![Fat Free CRM](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat Free CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | 基于 Ruby on Rails 的开源 CRM。 | Bitnami |
| ![GitLab Community Edition](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | 快速、安全的 Git 管理软件。 | Bitnami |
| ![Ghost](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | 专用于发布的平台。 | Bitnami |
| ![Hadoop](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 适用于可靠、可伸缩分布式计算的框架。 | Bitnami |
| ![HHVM](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 完全集成且已做好运行准备的开发环境。 | Bitnami |
| ![Horde Groupware Webmail](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [Horde Groupware Webmail](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 基于浏览器的企业就绪型免费通信套件。 | Bitnami |
| ![Jenkins](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | 支持以下 SCM 工具的集成服务器：CVS、 Subversion 和 Git。 | Bitnami |
| ![Jenkins 群集](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-jenkins) | Jenkins CI 是一个开源的持续集成服务器。  **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![JFrog Artifactory](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [JFrog Artifactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 由领先的发布者提供的二进制存储库软件。 | Bitnami |
| ![Joomla](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | 用户友好型 CMS，可以方便地生成网站。 | Bitnami |
| ![JRuby](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | Ruby 的高性能 Java 实现。 | Bitnami |
| ![Kafka](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | 强大的分布式发布-订阅消息传送系统。 | Bitnami |
| ![Kafka 群集](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka-cluster) | Apache Kafka 是一种发布-订阅式消息传送，已经重新构思为一种分布式提交日志。 此解决方案通过预配多个 Kafka 中转站和 Zookeeper 实例来改进 Kafka 群集的可靠性。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![LAMP](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 完全集成且已做好运行准备的开发环境。 | Bitnami |
| ![LAPP](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | 完整的 PHP、PostgreSQL 和 Apache 开发环境。 | Bitnami |
| ![Let's Chat](media/azure-stack-marketplace-azure-items/letschat.png) | [Let's Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | 开源持久消息传送应用程序。 | Bitnami |
| ![LimeSurvey](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | 问答轮询管理系统。 | Bitnami |
| ![Live Helper Chat](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [Live Helper Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | 开源实时聊天支持。 | Bitnami |
| ![Mahara](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | 常用的开源 ePortfolio 和社交网络 Web 应用。 | Bitnami |
| ![Magento](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | 常用的电子商务软件和平台。 | Bitnami |
| ![Mantis](media/azure-stack-marketplace-azure-items/mantis.png) | [Mantis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 高级 Bug 跟踪系统。 | Bitnami |
| ![MariaDB with Replication](media/azure-stack-marketplace-azure-items/mariadb.png) | [MariaDB with Replication](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mariadb-cluster) | MariaDB 是一种社区开发的开源 SQL 数据库服务器，因其企业功能、灵活性以及与领先科技公司的合作而在全世界广泛使用。 此解决方案使用多个 VM 将数据库从主节点复制到多个可配置的副本。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。**| Bitnami |
| ![Mattermost Team Edition](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost Team Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | 开源工作区消息传送解决方案。 | Bitnami |
| ![Mautic](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | 开源企业营销自动化平台。 | Bitnami |
| ![MEAN](media/azure-stack-marketplace-azure-items/mean.png) | [MEAN](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | 适用于 mongoDB 和 Node.js 的常用开发环境。 | Bitnami |
| ![MediaWiki](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 功能强大的可缩放 Wiki 实现。 | Bitnami |
| ![Memcached](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 高性能分布式内存对象缓存系统。 | Bitnami |
| ![Memcached Multiple Instances](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached Multiple Instances](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached-multipleinstances) | Memcached 是高性能分布式内存对象缓存系统。 此解决方案通过预配多个 Memcached 节点，为应用程序创建高性能且能抵御故障的分布式缓存。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![MODX](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 直观的 Web CMS。 | Bitnami |
| ![MongoDB](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | 以 C++ 编写的高性能开源 NoSQL 数据库。 | Bitnami |
| ![MongoDB with Replication](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB with Replication](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-mongodb) | 以 C++ 编写的高性能开源 NoSQL 数据库。 **此解决方案模板要求 Azure Stack 版本为 1807 或更高版本，并且还需要 Debian 8 和用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Moodle](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | 为在线学习社区设计的有效 CMS。 | Bitnami |
| ![MyBB](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 免费的开源论坛软件。 | Bitnami |
| ![MySQL](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 最常用的数据库系统。 | Bitnami |
| ![MySQL with Replication](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL with Replication](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql-cluster) | MySQL 是一种快速、可靠、可缩放且易用的开源关系数据库系统。 MySQL Server 适用于任务关键型重负荷生产系统，并且可以嵌入到大规模部署软件中。 此解决方案使用多个 VM 将数据库从主节点复制到多个可配置的副本。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Neos](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 通用的开源内容管理系统。 | Bitnami |
| ![Nginx](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | 完整的 PHP、 MySQL 和 Nginx 开发环境。 | Bitnami |
| ![Noalyss](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 功能强大的复式记帐系统。 | Bitnami |
| ![Node.js](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | 以 Javascript 编写的易于生成的开源环境。 | Bitnami |
| ![Odoo](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | 可以有效地连接业务流程的 ERP 和 CRM 系统。 | Bitnami |
| ![Open Atrium](media/azure-stack-marketplace-azure-items/openatrium.png) | [Open Atrium](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 灵活且多面的 Intranet 平台。 | Bitnami |
| ![OpenCart](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | 适用于在线商家的免费电子商务平台。 | Bitnami |
| ![Open edX](media/azure-stack-marketplace-azure-items/openedx.png) | [Open edX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 由领先的发布者提供的电子教学软件。 | Bitnami |
| ![OpenFire](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | 使用 XMPP 的开源实时协作服务器。 | Bitnami |
| ![OpenProject](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | 常用的开源项目管理软件。 | Bitnami |
| ![OrangeHRM](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | 包含丰富模块的 HR 管理系统。 | Bitnami |
| ![OroCRM](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 灵活的开源 CRM 应用程序。 | Bitnami |
| ![Osclass](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 在不需技术知识的情况下创建和管理免费分类广告。 | Bitnami |
| ![ownCloud](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | 常用开源文件同步和共享解决方案。 | Bitnami |
| ![OXID eShop](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID eShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 受信任的开源电子商务系统。 | Bitnami |
| ![phpBB](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | 可自定义的公告板解决方案。 | Bitnami |
| ![phpList](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 单向电子邮件公告传送系统。 | Bitnami |
| ![Pimcore](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 功能强大的业务管理平台 (CEM/CXM)。 | Bitnami |
| ![Piwik](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | 实时 Web 分析软件程序。 | Bitnami |
| ![Plone](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 免费开源虚拟设备。 | Bitnami |
| ![Pootle](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 适用于翻译项目的易用 Web 门户。 | Bitnami |
| ![PostgreSQL](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 很高级的开源数据库。 | Bitnami |
| ![PostgreSQL with Replication](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgres-cluster) | PostgreSQL (Postgres) 是一种开源对象-关系数据库系统。 ACID 合规，它支持外键、 联接、 视图、 触发器和存储的过程。 它以可靠性和数据完整性闻名。 此解决方案使用多个 VM 将数据库从主节点复制到多个可配置的副本。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![PrestaShop](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | 开源电子商务网站生成器。 | Bitnami |
| ![Process Maker 社区版](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker 社区版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | 业务流程管理和工作流自动化平台。 | Bitnami |
| ![Process Maker 企业版](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker 企业版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | 开源工作流和业务流程管理软件。 | Bitnami |
| ![ProcessWire](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | 常用 PHP5 开源 CMS。 | Bitnami |
| ![Publify](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | 基于 Ruby on Rails 的博客创建平台。 | Bitnami |
| ![RabbitMQ](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 提供通用平台的高效消息传递代理。 | Bitnami |
| ![RabbitMQ 群集](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq-cluster) | RabbitMQ 是一种消息传送中转站，为应用程序提供通用的消息收发平台，允许消息安全地存放在其中，直至被接收。 此解决方案使用多个 VM 在 RabbitMQ 群集中预配多个节点，形成单一的逻辑中转站。 **此解决方案模板还需要 Debian 8 以及适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Re:dash](media/azure-stack-marketplace-azure-items/redash.png) | [Re:dash](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | 开源数据可视化和协作平台。 | Bitnami |
| ![Redis](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 功能强大的开源键-值存储。 | Bitnami |
| ![Redis 高可用性](media/azure-stack-marketplace-azure-items/redis.png) | [Redis 高可用性](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis-cluster) | 功能强大的开源键-值存储。 **此解决方案模板要求 Azure Stack 版本为 1807 或更高版本，并且还需要 Debian 8 和用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Redmine](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 强大的项目管理 Web 应用 | Bitnami |
| ![Redmine+Agile](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine+Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | 使用 Agile 插件预先配置的项目管理应用程序。 | Bitnami |
| ![ReportServer 社区](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer 社区](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | 开源商业智能平台。 | Bitnami |
| ![ReportServer Enterprise](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | 企业商业智能平台。 | Bitnami |
| ![ResourceSpace](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | 改进了协作的数字资产管理系统。 | Bitnami |
| ![Roundcube](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | 基于浏览器的 IMAP 客户端，包含 MIME 支持之类的功能。 | Bitnami |
| ![Ruby](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | 适用于 Ruby on Rails 的易用开发环境。 | Bitnami |
| ![SEO Panel](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO Panel](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 适用于跟踪多个网站的开源 SEO 管理应用程序。 | Bitnami |
| ![Shopware](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | 开源电子商务平台。 | Bitnami |
| ![Simple Machines Forum](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [Simple Machines Forum](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 简单的论坛软件，适用于生成自己的在线论坛。 | Bitnami |
| ![Spree](media/azure-stack-marketplace-azure-items/spree.png) | [Spree](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 易用的电子商务平台。 | Bitnami |
| ![Subversion](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | 开源版本控制系统。 | Bitnami |
| ![SuiteCRM](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | 常用的企业级 CRM 应用程序。 | Bitnami |
| ![TestLink](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 测试管理软件，增强质量保证。 | Bitnami |
| ![Tiki Wiki CMS Groupware](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS Groupware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 完备的 Wiki 平台。 | Bitnami |
| ![Tiny Tiny RSS](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [Tiny Tiny RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 基于 Web 的新闻源和聚合器，灵活且开源。 | Bitnami |
| ![Tomcat](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | 实现 Java 社区规范的常用平台。 | Bitnami |
| ![Trac](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 增强的 Wiki 和问题跟踪系统。 | Bitnami |
| ![Typo3](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 十分灵活的 CMS。 | Bitnami |
| ![Weblate](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | 基于 Web 的翻译管理系统。 | Bitnami |
| ![WebMail Pro PHP](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [WebMail Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | 带企业版功能的 Webmail 系统。 | Bitnami |
| ![WildFly](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | 包括 Apache、WildFly、MySQL 和 Java 在内的应用程序服务器。 | Bitnami |
| ![WordPress](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 最常用且已准备就绪的 CMS。 | Bitnami |
| ![Xoops](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | CMS 和 Web 门户程序，适用于创建动态网站。 | Bitnami |
| ![Zurmo](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | 开源 CRM 系统：移动、 社交和 Gamified。 | Bitnami |
| ![Check Point vSEC Security Management](media/azure-stack-marketplace-azure-items/checkpoint.png) | [Check Point vSEC Security Management](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | Check Point vSEC 提供高级的多层威胁防护，可以保护客户在 Azure 中的资产免受恶意软件和复杂威胁的损害。 **下载两种 Check Point vSEC 市场项。** | 检查点 |
| ![Chef Automate](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | 使用 Chef Automate 这种持续自动化平台进行生成、部署和管理。 **这两种 Chef marketplace 项下载**。 | Chef Software, Inc |
| ![Commvault](media/azure-stack-marketplace-azure-items/commvault.png) | [Commvault](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault) | 备份和恢复、 应用程序和虚拟机迁移到 Azure Stack 和 Azure Stack 环境的单个解决方案中的灾难恢复综合解决方案。 | Commvault |
| ![CloudLink SecureVM](media/azure-stack-marketplace-azure-items/cloudlink.png) | [CloudLink SecureVM](https://azuremarketplace.microsoft.com/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 VM。 **下载所有 CloudLink SecureVM 项。** | Dell EMC |
| ![EventTracker SIEM](media/azure-stack-marketplace-azure-items/eventtracker.png) | [EventTracker SIEM](https://azuremarketplace.microsoft.com/marketplace/apps/eventtracker.eventtracker-siem)  | EventTracker SIEM 是一种综合性安全平台，提供具有审核就绪型符合性功能的高级安全工具。 | EventTracker |
| ![Exivity-混合云计费解决方案](media/azure-stack-marketplace-azure-items/exivity.png) | [Exivity-混合云计费解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/exivity.exivity-vm)  | 将模型能够满足的要求几乎任何 IT 服务交付的计费工具，无论是在本地、 公有云或混合环境中部署。 | Exivity |
| ![f5](media/azure-stack-marketplace-azure-items/f5.png) | [f5 Big-IP 性能版本](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best)  | 高级负载平衡，GSLB、 网络防火墙、 DNS、 WAF 和应用程序访问权限。 | F5 网络 |
| ![FortiGate 下一代防火墙](media/azure-stack-marketplace-azure-items/fortinetsquare.png) | [FortiGate 下一代防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-fortigate-singlevm) | 防火墙技术，可提供与一套全面的强大的安全功能的完整内容和网络保护。 应用程序控制、 防病毒、 IPS、 web 筛选和 VPN 及高级功能，例如漏洞管理它们协同工作以识别并减小最新的复杂安全威胁。 | Fortinet |
| ![Hortonworks 数据平台 (HDP) 沙盒](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks 数据平台 (HDP) 沙盒](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | 由 HDP 2.5 100% 开源平台提供支持，适用于 Hadoop、Spark、Storm、HBase、Kafka、Hive、Ambari。 | Hortonworks |
| ![Kaspersky](media/azure-stack-marketplace-azure-items/kaspersky.png) | [Kaspersky 的混合云安全性](https://azuremarketplace.microsoft.com/marketplace/apps/kaspersky_lab.kaspersky_hybrid_cloud_security?tab=Overview) | Kaspersky 的混合云安全性启用无缝地协调和自适应网络安全生态系统。 | Kaspersky 实验室 |
| ![KEMP LoadMaster 负载均衡器 ADC 内容交换机](media/azure-stack-marketplace-azure-items/kemp.png) | [KEMP LoadMaster 负载均衡器 ADC 内容交换机](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | 第 4-7 层应用程序传送控制器 (ADC) 负载均衡器、内容交换机和流量管理器。 | KEMP Technologies Inc. |
| ![Kubernetes](media/azure-stack-marketplace-azure-items/kubernetes.png) | [Kubernetes](azure-stack-solution-template-kubernetes-cluster-add.md) | 此解决方案部署与使用 AKS 引擎生成的模板作为独立群集运行的 Kubernetes 群集。<br>**此解决方案模板还要求 Ubuntu Server 16.04 LTS 和自定义脚本 Linux 2.0。**| Microsoft |
| ![Service Fabric 群集](media/azure-stack-marketplace-azure-items/servicefrabric.png) | [Service Fabric 群集](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceFabricCluster?tab=Overview) | 此解决方案部署 Service Fabric 独立群集以在虚拟机规模集上运行。 <br>**此解决方案模板需要下载 Windows Server 2016 Datacenter**| Microsoft |
| ![NRI mPLAT Suite - Multi-Cloud Conductor](media/azure-stack-marketplace-azure-items/nri.png) | [mPLAT Suite - Multi-Cloud Conductor](https://azuremarketplace.microsoft.com/marketplace/apps/nri.mplatmc2018-vm) | 单一虚拟管理平台，用于监视、配置、预配、自动化和控制任何工作负荷或云。 | NRI |
| ![NooBaa Hybrid AWS S3 compatible - Community Edition](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa Hybrid AWS S3 兼容-Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/noobaa.noobaa-hybrid-s3-archive-05) | 兼容 S3 的存储服务，涵盖公用的和本地的容量资源。 | NooBaa |
| ![Multipoint，零 NetFoundry 网关信任 Azure Stack 连接](media/azure-stack-marketplace-azure-items/netfoundry.png) | [Multipoint，零 NetFoundry 网关信任 Azure Stack 连接](https://azuremarketplace.microsoft.com/marketplace/apps/tata_communications.netfoundry_cloud_gateway) | Azure Stack 和任何位置通过任何网络连接，通过行业领先零信任安全，5 倍的吞吐量的 VPN 和无限多个并发用户之间的纯软件、 多点连接。 | NetFoundry |
| ![Palo Alto VM 系列下一代防火墙](media/azure-stack-marketplace-azure-items/paloalto.png) | [Palo Alto VM 系列下一代防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/paloaltonetworks.vmseries-ngfw) | VM 系列下一代防火墙可以让客户安全地将其应用程序和数据迁移到 Azure Stack，使用应用程序允许列表和威胁防范策略，使客户能够防范已知的和未知的威胁。 **此映像需要模板来部署；请参阅此[文章](https://www.paloaltonetworks.com/documentation/81/virtualization/virtualization/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewalls-on-azure-stack)以获取重要信息。**| Palo Alto Networks, Inc. |
| ![PT 应用程序防火墙](media/azure-stack-marketplace-azure-items/pt.png) | [PT 应用程序防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/ptsecurity.ptaf) | PT 应用程序防火墙检测已知的和未知的漏洞，防范对 Web 应用的攻击。 **下载两种 PT 市场项。** | Positive Technologies |
| ![Puppet Enterprise](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | 使用 Puppet Enterprise 可以实现 Azure Stack 基础结构整个生命周期的自动化。 **下载两种 Puppet 市场项。** | Puppet |
| ![Quest Rapid Recovery](media/azure-stack-marketplace-azure-items/quest.png) | [Quest Rapid Recovery Core](https://azuremarketplace.microsoft.com/marketplace/apps/quest.rapid-recovery-core-vm) | 备份、 复制和恢复一个易于使用的软件解决方案中，统一了高级数据保护的快速恢复。 | Quest Software |
| ![SUSE Manager 3.1 Proxy (BYOS)](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.1 Proxy (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/suse.suse-manager-proxy-byos?tab=Overview) | 同类最佳开源基础结构管理。 | SUSE |
| ![CipherTrust 云密钥管理器](media/azure-stack-marketplace-azure-items/cckm.png) | [CipherTrust 云密钥管理器](https://azuremarketplace.microsoft.com/marketplace/apps/thales-vormetric.ciphertrust-ckm) | 利用 Microsoft Azure 和其他云提供商将你自己的密钥 (BYOK) Api，CipherTrust 云密钥管理器可减少密钥管理的复杂性和运营成本向您提供的加密密钥的余地生命周期控制集中式管理和可见性。 | Thales 网络安全 |
| ![Veeam 备份和复制](media/azure-stack-marketplace-azure-items/veeam.png) | [Veeam 备份和复制](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=Overview) | Veeam® 备份和 Replication™ 可帮助企业实现对所有工作负荷的全面的数据保护 — 虚拟、 物理和基于云的。 通过单一控制台中，可以实现快速、 灵活且可靠的备份、 恢复和复制的所有应用程序和数据。 | Veeam 软件 |
| ![ZeroDown Software BCaaS](media/azure-stack-marketplace-azure-items/zerodown.png) | [ZeroDown Software Business Continuity as a Service](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.bcaasforazure) | ZeroDown® 软件技术提供了企业对其业务连续性通过其公司数据的持续访问权限，即服务 (BCaaS)™ 体系结构、 保护应用程序和事务，如果网络中断会发生，通常会会削弱企业。 | ZeroDown Software |
