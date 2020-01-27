---
title: 适用于 Azure Stack 集线器的 Azure Marketplace 项 |Microsoft Docs
description: 了解哪些 Azure Marketplace 项可在 Azure Stack 集线器中使用。
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
ms.date: 01/21/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 01/21/2020
ms.openlocfilehash: 7a8fb4c500f749bfe58e1adcb7e5a9ce854bb9d0
ms.sourcegitcommit: 10520f4481c0e3891c9dcd46fb21249ad2a33717
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76516754"
---
# <a name="azure-marketplace-items-available-for-azure-stack-hub"></a>适用于 Azure Stack 集线器的 Azure Marketplace 项

## <a name="virtual-machine-extensions"></a>虚拟机扩展

当你使用的虚拟机（VM）扩展有更新时，你应该下载它们。 产品中随附的扩展不会在正常修补和更新过程中更新，因此请经常检查更新。 其他扩展只能通过 Marketplace 管理提供。

|  | 项名称 | 说明 | 发布者 | 操作系统类型 |
| --- | --- | --- | --- | --- |
|![SQL IaaS 扩展（SqlIaasExtension）](media/azure-stack-marketplace-azure-items/cse.png) | [SQL IaaS 扩展（SqlIaasExtension）](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| **下载此扩展以在 Windows Marketplace 项目上部署任何 SQL Server-需要此扩展。** | Microsoft | Windows |
|![自定义脚本扩展](media/azure-stack-marketplace-azure-items/cse.png) | [自定义脚本扩展](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript)| **将此更新下载到适用于 Windows 的自定义脚本扩展的内置版本。** | Microsoft | Windows |
|![PowerShell DSC 扩展](media/azure-stack-marketplace-azure-items/dsc.png) | [PowerShell DSC 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| **将此更新下载到 PowerShell DSC 扩展的内置版本。已更新为支持 TLS 1.2。** | Microsoft | Windows |
| ![Microsoft 反恶意软件扩展](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft 反恶意软件扩展](https://docs.microsoft.com/azure/security/azure-security-antimalware)| 适用于 Azure 的 Microsoft 反恶意软件是适用于应用程序和租户环境的单代理解决方案，可在后台运行而无需人工干预。 **将此更新下载到反恶意软件扩展的内置版本。** | Microsoft | Windows |
| ![Microsoft Azure 诊断扩展](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft Azure 诊断扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-windows)| Microsoft Azure 诊断是 Azure 中的一种功能，可用于在已部署的应用上收集诊断数据。 **将此更新下载到适用于 Windows 的诊断扩展的内置版本。** | Microsoft | Windows |
| ![Azure Monitor、更新和配置管理扩展](media/azure-stack-marketplace-azure-items/cse.png) | [Azure Monitor、更新和配置管理扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)| Azure Monitor、更新和配置管理扩展与 Log Analytics、Azure 安全中心和 Azure Sentinel 一起使用，以提供 VM 监视功能。 **将此更新下载到适用于 Windows 的监视代理扩展的内置版本。** | Microsoft | Windows |
|![自定义脚本扩展](media/azure-stack-marketplace-azure-items/cse.png) | - [自定义脚本扩展（版本1，已弃用）](https://docs.microsoft.com/azure/virtual-machines/extensions/custom-script-linuxostc) </b> - [自定义脚本扩展（版本2）](https://docs.microsoft.com/azure/virtual-machines/extensions/custom-script-linux) |**将此更新下载到适用于 Linux 的自定义脚本扩展的内置版本。此扩展有多个版本，应同时下载1.5.2.1 和 2.0. x** | Microsoft | Linux |
| ![适用于 Linux 的 VM 访问](media/azure-stack-marketplace-azure-items/cse.png) | [适用于 Linux 的 VM 访问](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| **将此更新下载到 VMAccess for Linux 扩展的内置版本。如果计划使用 Debian Linux Vm，则此更新很重要。** | Microsoft | Linux |
| ![适用于 Linux 的 Acronis 备份扩展](media/azure-stack-marketplace-azure-items/acronis.png) | [适用于 Linux 的 Acronis 备份扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | 用于 Microsoft Azure 的 Acronis 备份扩展是数据保护产品 Acronis 备份系列的一部分。 | Acronis 国际 GmbH。 | Linux |
| ![适用于 Windows 的 Acronis 备份扩展](media/azure-stack-marketplace-azure-items/acronis.png) | [适用于 Windows 的 Acronis 备份扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | 用于 Microsoft Azure 的 Acronis 备份扩展是数据保护产品 Acronis 备份系列的一部分。 | Acronis 国际 GmbH。 | Windows |
| ![适用于 Linux 的 CloudLink SecureVM 扩展](media/azure-stack-marketplace-azure-items/cloudlink.png) | [适用于 Linux 的 CloudLink SecureVM 扩展](https://azuremarketplace.microsoft.com/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 Vm。 | Dell EMC | Linux |
| ![适用于 Windows 的 CloudLink SecureVM 扩展](media/azure-stack-marketplace-azure-items/cloudlink.png) | [适用于 Windows 的 CloudLink SecureVM 扩展](https://azuremarketplace.microsoft.com/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 Vm。 | Dell EMC | Windows |
| ![Kaspersky](media/azure-stack-marketplace-azure-items/kaspersky.png) | [适用于 Windows 的 kaspersky 等混合云安全代理](https://azuremarketplace.microsoft.com/marketplace/apps/KasperskyLab.ksws-windows-arm?tab=Overview) | 通过 Kaspersky 等混合云安全，可以通过 Azure 扩展在云工作负荷内预配网络安全功能。 | Kaspersky 等实验室 | Windows |
| ![Kaspersky](media/azure-stack-marketplace-azure-items/kaspersky.png) | [适用于 Linux 的 kaspersky 等混合云安全代理](https://azuremarketplace.microsoft.com/marketplace/apps/KasperskyLab.kesl-linux-arm?tab=Overview) | 通过 Kaspersky 等混合云安全，你可以通过 Azure 扩展在云工作负荷内预配网络安全功能。 | Kaspersky 等实验室 | Linux |

## <a name="microsoft-vm-images-and-solution-templates"></a>Microsoft VM 映像和解决方案模板

Microsoft Azure Stack 中心支持以下 Azure Marketplace Vm 和解决方案模板。 如上所述，单独下载任何依赖项。 诸如 SQL Server 和 Machine Learning Server 之类的应用需要适当的许可，但标记为免费或试用版除外。

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![Windows Server](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer) | 易于部署、经济高效、以应用为中心的企业级解决方案，以及以用户为中心的解决方案。 这些映像会定期更新，其中包含最新的修补程序。 **重要信息：必须先删除2018年1月18日之前下载的映像，并将其替换为最新版本。** | Microsoft |
| ![SharePoint Server 2013 试用版](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 试用版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsharepoint.microsoftsharepointserver) | Windows Server 2012 Datacenter 和 Visual Studio 2019 社区版上的 Microsoft SharePoint Server 2013 试用版。 | Microsoft |
| ![SharePoint Server 2016 试用版](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 试用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Windows Server 2016 Datacenter Microsoft SharePoint Server 2016 试用版。 | Microsoft |
| ![Windows Server 2012 R2 上的 SQL Server 2014 SP3](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2012 R2 上的 SQL Server 2014 SP3](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2) | SQL Server 2014 Service Pack 2。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2016 SP1 开发人员](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2016 SP1 开发人员](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp1-ws2016) | 适用于事务、数据仓库、商业智能和分析工作负荷的免费开发人员版本 SQL Server 2016 SP1。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2016 SP1 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2016 SP1 Express](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp1-ws2016) | SQL Server 2016 SP1 的免费 express 版本。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP1 标准版](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 标准版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp1-ws2016) | 用于智能任务关键型应用程序的数据库平台。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp1-ws2016) | 用于智能任务关键型应用程序的数据库平台。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 开发人员](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 开发人员](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | 适用于事务、数据仓库、商业智能和分析工作负荷的免费开发人员版本 SQL Server 2016 SP2。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2016 SP2 Express](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | SQL Server 2016 SP2 的免费 express 版本。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP2 标准版](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP2 标准版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | 用于智能任务关键型应用程序的数据库平台。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2016 SP2 Enterprise](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP2 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) | 用于智能任务关键型应用程序的数据库平台。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2017 开发人员](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2017 开发人员](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | 适用于事务、数据仓库、商业智能和分析工作负荷的 SQL Server 2017 的免费开发人员版本。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![免费许可证：Windows Server 2016 上的 SQL Server 2017 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：Windows Server 2016 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | SQL Server 2017 的免费 express 版本。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2017 标准版](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 标准版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | 用于智能任务关键型应用程序的数据库平台。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Windows Server 2016 上的 SQL Server 2017 企业版](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 企业版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2016) | 用于智能任务关键型应用程序的数据库平台。 **需要下载：** SQL IaaS 扩展。 | Microsoft |
| ![Ubuntu Server 16.04 LTS 上的 SQL Server 2017](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 企业版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ubuntu1604) | 用于智能任务关键型应用程序的数据库平台。 | Microsoft + 规范 |
| ![SUSE Linux Enterprise Server （SLES） 12 SP2 上的 SQL Server 2017 标准](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server （SLES） 12 SP2 上的 SQL Server 2017 标准](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonSLES12SP2?tab=Overview) | 用于智能任务关键型应用程序的数据库平台。 | Microsoft + SUSE |
| ![免费许可证：SQL Server 2017 开发人员 SUSE Linux Enterprise Server （SLES） 12 SP2](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：SQL Server 2017 开发人员 SUSE Linux Enterprise Server （SLES） 12 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2?tab=Overview) | 适用于事务、数据仓库、商业智能和分析工作负荷的 SQL Server 2017 的免费开发人员版本。 | Microsoft + SUSE |
| ![免费许可证：SUSE Linux Enterprise Server （SLES） 12 SP2 上的 SQL Server 2017 Express](media/azure-stack-marketplace-azure-items/sql.png) | [免费许可证：SUSE Linux Enterprise Server （SLES） 12 SP2 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | SQL Server 2017 的免费 express 版本。 | Microsoft + SUSE |
| ![SQL Server 2017 企业版 SUSE Linux Enterprise Server （SLES） 12 SP2](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2017 企业版 SUSE Linux Enterprise Server （SLES） 12 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseonSLES12SP2?tab=Overview) | 用于智能任务关键型应用程序的数据库平台。 | Microsoft + SUSE |
| ![SQL Server 2017 SUSE Linux Enterprise Server （SLES） 12 SP2 上的 Web](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2017 SUSE Linux Enterprise Server （SLES） 12 SP2 上的 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonSLES12SP2) | 用于智能任务关键型应用程序的数据库平台。 | Microsoft + SUSE |
| ![Windows Server 2016 上的 Microsoft Machine Learning Server 9.3。0](media/azure-stack-marketplace-azure-items/microsoft.png) | [Windows Server 2016 上的 Microsoft Machine Learning Server 9.3。0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onWindowsServer2016) | Microsoft Machine Learning Server Windows Server 2016 上的9.3.0。 | Microsoft |
| ![Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3。0](media/azure-stack-marketplace-azure-items/microsoft.png) | [Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3。0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onUbuntu1604) | Microsoft Machine Learning Server Ubuntu 16.04 上的9.3.0。 | Microsoft + 规范 |
| ![CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3。0](media/azure-stack-marketplace-azure-items/microsoft.png) | [CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3。0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onCentOSLinux72) | Microsoft Machine Learning Server CentOS Linux 7.2 上的9.3.0。 | Microsoft + Rogue Wave |

## <a name="linux-distributions"></a>Linux 分发版

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![清除 Linux OS](media/azure-stack-marketplace-azure-items/clearlinux.png) | [清除 Linux OS](https://azuremarketplace.microsoft.com/marketplace/apps/clear-linux-project.clear-linux-os) | 针对 Intel 体系结构进行了优化的引用 Linux 分发版。 | 清除 Linux 项目 |
| ![CoreOS Linux (Stable)](media/azure-stack-marketplace-azure-items/coreos.png) | [CoreOS Linux （稳定）](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | CoreOS 是一种新式的最小 Linux 分发版，提供一种简单的方法来运行容器、管理群集并无缝更新服务器-所有启用了仓库规模计算的组件。 | CoreOS |
| ![Ubuntu Server](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server 是全球流行的 Linux 云环境。 | 规范 |
| ![Debian 8 "Jessie"](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux 是最流行的 Linux 分发版之一。 | credativ |
| ![Oracle Linux](media/azure-stack-marketplace-azure-items/oracle.png) | [Oracle Linux](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oracle-linux) | Oracle Linux 操作系统是针对开放云基础结构设计的。 它为企业 SaaS 和 PaaS 工作负荷以及传统的企业应用提供领先的性能、可伸缩性和可靠性。 | Oracle |
| ![基于 CentOS 的 6.8](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的6。8](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased68HPC) | Linux 的这种分发基于 CentOS，由流氓 Wave 软件提供。 | Rogue Wave Software （以前称为 OpenLogic）  |
| ![基于 CentOS 的6.10](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的6.10](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased610) | Linux 的这种分发基于 CentOS，由流氓 Wave 软件提供。 | Rogue Wave Software （以前称为 OpenLogic）  |
| ![基于 CentOS 的 7.3](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的7。3](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC) | Linux 的这种分发基于 CentOS，由流氓 Wave 软件提供。 | Rogue Wave Software （以前称为 OpenLogic） |
| ![基于 CentOS 的7。5](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的7。5](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased75) | Linux 的这种分发基于 CentOS，由流氓 Wave 软件提供。 | Rogue Wave Software （以前称为 OpenLogic） |
| ![基于 CentOS 的 7.5-LVM](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 7.5-LVM](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased75) | Linux 的这种分发基于 CentOS，由流氓 Wave 软件提供。 | Rogue Wave Software （以前称为 OpenLogic） |
| ![SLES 11 SP4 （BYOS）](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 11 SP4 （BYOS）](https://azuremarketplace.microsoft.com/marketplace/apps/suse.sles-byos) | SUSE Linux Enterprise Server 11 SP4。 | SUSE |
| ![SLES 12 SP3 （BYOS）](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP4 （BYOS）](https://azuremarketplace.microsoft.com/marketplace/apps/suse.sles-byos) | SUSE Linux Enterprise Server 12 SP4。 | SUSE |
| ![SLES 15 （BYOS）](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 15 （BYOS）](https://azuremarketplace.microsoft.com/marketplace/apps/suse.sles-byos) | SUSE Linux Enterprise Server 15。 | SUSE |

## <a name="third-party-byol-free-trial-images-and-solution-templates"></a>第三方 BYOL、免费版、试用版映像和解决方案模板

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![A10 vThunder ADC](media/azure-stack-marketplace-azure-items/a10.png) | [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1) | 适用于 Microsoft Azure 的 A10 网络 vThunder ADC （应用程序传送控制器）专用于实现高性能、灵活性和易于部署的应用交付以及服务器负载平衡，并经过优化，可在 Azure 云中本机运行。 | A10 Networks |
| ![Arista vEOS 路由器](media/azure-stack-marketplace-azure-items/arista.png) | [Arista vEOS 路由器](https://azuremarketplace.microsoft.com/marketplace/apps/arista-networks.veos-router) | Arista vEOS 路由器是一种功能丰富的多云和多虚拟机监控程序虚拟路由器，使企业和云提供商能够构建一致、高度安全和可缩放的混合网络。 | Arista 网络 |
| ![Barracuda 应用程序安全控制中心](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda 应用程序安全控制中心](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-app-sec-control-center) | 集中管理多个 Barracuda Web 应用程序防火墙（WAF）。 | Barracuda Networks, Inc. |
| ![Barracuda 电子邮件安全网关](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda 电子邮件安全网关](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-email-security-gateway) | 电子邮件安全网关，可防范入站电子邮件威胁。 | Barracuda Networks, Inc. |
| ![Barracuda Web 应用程序防火墙（WAF）](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Web 应用程序防火墙（WAF）](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf) | 针对自动 & 目标攻击的安全性和 DDoS 防护。 | Barracuda Networks, Inc. |
| ![Barracuda CloudGen 防火墙控制中心](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda CloudGen 防火墙控制中心](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-cc) | 集中管理数百个 Barracuda CloudGen 防火墙，而不考虑它们的位置和外形规格。 | Barracuda Networks, Inc. |
| ![适用于 Azure 的 Barracuda CloudGen 防火墙](media/azure-stack-marketplace-azure-items/barracuda.png) | [适用于 Azure 的 Barracuda CloudGen 防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-ngf) | 提供防火墙保护，其中应用程序和数据的驻留位置，而不是仅限连接终止的位置。 | Barracuda Networks, Inc. |
| ![AbanteCart](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | 开源电子商务购物车。 | Bitnami |
| ![ActiveMQ](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | Java 中的开源消息代理。 | Bitnami |
| ![Akeneo](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 旨在简化管理过程的强大 PIM。 | Bitnami |
| ![Alfresco 社区](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco 社区](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | 用于轻松进行文档管理的 ECM 系统。 | Bitnami |
| ![Apache Solr](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 可靠的开源企业搜索平台。 | Bitnami |
| ![Canvas LMS](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | 开源学习管理系统。 | Bitnami |
| ![Cassandra](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 具有高可用性的可缩放开源数据库。 | Bitnami |
| ![Cassandra 群集](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra-cluster) | Apache Cassandra 是一种开源分布式数据库管理系统，旨在跨多个商用服务器处理大量数据，从而提供高可用性且无单点故障。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![CiviCRM](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | 简单的基于 web 的关系管理系统。 | Bitnami |
| ![CMS 简单](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [CMS 简单](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | 创建和管理网站的快速而简单的方法。 | Bitnami |
| ![Composr](media/azure-stack-marketplace-azure-items/composr.png) | [Composr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ocportal) | Composr 是包含高级内容、社交、交互式和动态功能的 CMS。 完全灵活、可通过主题和扩展：适用于几乎任何类型的网站。 | Bitnami |
| ![Concrete5](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | 轻松部署 web 应用、网站、存储和论坛。 | Bitnami |
| ![Coppermine](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 多用途、功能完备的 web 库。 | Bitnami |
| ![CouchDB](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 易于使用的开源数据库系统。 | Bitnami |
| ![Diaspora](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | 常用的个人 web 服务器。 | Bitnami |
| ![Discourse](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 高分辨率开源讨论平台。 | Bitnami |
| ![Django](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 高级 Python Web 框架。 | Bitnami |
| ![Dolibarr](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 免费的开源软件包。 | Bitnami |
| ![DokuWiki](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 通用的开源 wiki 软件。 | Bitnami |
| ![DreamFactory](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | 开源 REST API 包含 SQL、NoSQL 和 BLOB 等服务。 | Bitnami |
| ![Drupal](media/azure-stack-marketplace-azure-items/drupal.png) | [Drupal](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.drupal) | Drupal 是世界上最常用的开源内容管理系统之一。 它已预先配置 Ctools 和 Views 模块，Drush，让我们加密自动配置支持。 | Bitnami |
| ![Elasticsearch](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 灵活且功能强大的开源分析引擎。 | Bitnami |
| ![ELK](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | 大数据套件，包括 Elasticsearch、Kibana 和 Logstash。 | Bitnami |
| ![ERPNext](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | 开源企业资源规划（ERP）平台。 | Bitnami |
| ![EspoCRM](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 有助于管理客户关系的简单 CRM 系统。 | Bitnami |
| ![etcd](media/azure-stack-marketplace-azure-items/etcd.png) | [etcd](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.etcd) | etcd 是一个分布式键-值存储，旨在在群集中安全存储数据。 etcd 在生产环境中广泛用于可靠性、容错和易用性。 | Bitnami |
| ![Exo 除外平台](media/azure-stack-marketplace-azure-items/exoplatform.png) | [Exo 除外平台](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 面向企业的开源、社交软件。 | Bitnami |
| ![Fat 免费 CRM](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat 免费 CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | 开源 Ruby on Rails CRM。 | Bitnami |
| ![GitLab 社区版](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab 社区版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | 快速安全的 Git 管理软件。 | Bitnami |
| ![Ghost](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | 专用于发布的平台。 | Bitnami |
| ![Grafana](media/azure-stack-marketplace-azure-items/grafana.png) | [Grafana](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.grafana) | Grafana 是一种开源分析和监视仪表板，适用于40多个数据源，包括 Graphite、Elasticsearch、Prometheus、MariaDB/MySQL、PostgreSQL、InfluxDB、OpenTSDB 等。 | Bitnami |
| ![Hadoop](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 适用于可靠、可缩放和分布式计算的框架。 | Bitnami |
| ![HHVM](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 完全集成且随时可用的开发环境。 | Bitnami |
| ![大量组件 Web 邮件](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [大量组件 Web 邮件](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 免费的、企业就绪的、基于浏览器的通信套件。 | Bitnami |
| ![Jenkins](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | 支持 SCM 工具的 Integration server：CVS、Subversion 和 Git。 | Bitnami |
| ![Jenkins 群集](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-jenkins) | Jenkins CI 是开源持续集成服务器。  **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![JFrog Artifactory](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [JFrog Artifactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 主发布服务器中的二进制存储库软件。 | Bitnami |
| ![Joomla](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | 用于简易网站构建的用户友好 CMS。 | Bitnami |
| ![JRuby](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | Ruby 的高性能 Java 实现。 | Bitnami |
| ![Kafka](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | 强大的分布式发布-订阅消息传送系统。 | Bitnami |
| ![Kafka 群集](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka-cluster) | Apache Kafka 发布-订阅消息传送 rethought 作为分布式提交日志。 此解决方案通过预配多个 Kafka 代理和 Zookeeper 实例来提高 Kafka 群集的可靠性。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![LAMP](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 完全集成并准备好运行开发环境。 | Bitnami |
| ![LAPP](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | 完整的 PHP、PostgreSQL 和 Apache 开发环境。 | Bitnami |
| ![聊天](media/azure-stack-marketplace-azure-items/letschat.png) | [聊天](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | 开源持久消息传递应用。 | Bitnami |
| ![LimeSurvey](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | 问答投票管理系统。 | Bitnami |
| ![Live Helper 聊天](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [Live Helper 聊天](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | 开源实时聊天支持。 | Bitnami |
| ![Mahara](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | 常用开源 ePortfolio 和社交网络 web 应用。 | Bitnami |
| ![Magento](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | 常用的电子商务软件和平台。 | Bitnami |
| ![Mantis](media/azure-stack-marketplace-azure-items/mantis.png) | [Mantis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 高级 bug 跟踪系统。 | Bitnami |
| ![MariaDB 与复制](media/azure-stack-marketplace-azure-items/mariadb.png) | [MariaDB 与复制](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mariadb-cluster) | MariaDB 是一种开放源代码社区开发的 SQL 数据库服务器，该服务器广泛使用，因为其企业功能、灵活性和与领先技术公司的协作。 此解决方案使用多个 Vm 将数据库从主节点复制到可配置的副本数。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。**| Bitnami |
| ![Mattermost 团队版](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost 团队版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | 开源工作区消息传递解决方案。 | Bitnami |
| ![Mautic](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | 开源企业营销自动化平台。 | Bitnami |
| ![格林威治时间](media/azure-stack-marketplace-azure-items/mean.png) | [格林威治时间](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | MongoDB 和 node.js 的流行开发环境。 | Bitnami |
| ![MediaWiki](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 强大的可扩展 wiki 实现。 | Bitnami |
| ![Memcached](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 高性能分布式内存对象缓存系统。 | Bitnami |
| ![Memcached 多个实例](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached 多个实例](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached-multipleinstances) | Memcached 是一种高性能分布式内存对象缓存系统。 此解决方案预配多个 Memcached 节点，为你的应用创建高性能、可经受故障的分布式缓存。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![MODX](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 直观的 Web CMS。 | Bitnami |
| ![MongoDB](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | 编写的C++高性能开源 NoSQL 数据库。 | Bitnami |
| ![带有复制的 MongoDB](media/azure-stack-marketplace-azure-items/mongodb.png) | [带有复制的 MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-mongodb) | 编写的C++高性能开源 NoSQL 数据库。 **此解决方案模板要求 Azure Stack 集线器处于版本1807或更高版本，并且还需要 Debian 8 和 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Moodle](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | 为联机学习社区设计的有效 CMS。 | Bitnami |
| ![MyBB](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 免费和开源论坛软件。 | Bitnami |
| ![MySQL](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 最流行的数据库系统。 | Bitnami |
| ![具有复制的 MySQL](media/azure-stack-marketplace-azure-items/mysql.png) | [具有复制的 MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql-cluster) | MySQL 是一种快速、可靠、可缩放且易于使用的开源关系数据库系统。 MySQL Server 适用于任务关键型、繁重的生产系统以及嵌入到大容量部署的软件。 此解决方案使用多个 Vm 将数据库从主节点复制到可配置的副本数。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![NAT](media/azure-stack-marketplace-azure-items/nats.png) | [NAT](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nats) | NAT 是一种开源、轻型和高性能的消息系统。 它非常适合用于分布式系统，并支持新式的云体系结构和发布-订阅、请求-答复和队列模型。 | Bitnami |
| ![Neos](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 通用开源内容管理系统。 | Bitnami |
| ![Neo4j](media/azure-stack-marketplace-azure-items/neo4j.png) | [Neo4j](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neo4j) | Neo4j 是一个高性能的图形存储，包含成熟且可靠的数据库中的所有功能，例如友好的查询语言和 ACID 事务。| Bitnami |
| ![Nginx](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | 完整的 PHP、MySQL 和 Nginx 开发环境。 | Bitnami |
| ![Noalyss](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 功能强大的双输入记帐系统。 | Bitnami |
| ![Node.js](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | 用 Javascript 编写的开源环境便于构建。 | Bitnami |
| ![Odoo](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | 有效连接业务流程的 ERP 和 CRM 系统。 | Bitnami |
| ![打开 Atrium](media/azure-stack-marketplace-azure-items/openatrium.png) | [打开 Atrium](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 灵活的多面 Intranet 平台。 | Bitnami |
| ![OpenCart](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | 适用于在线商家的免费电子商务平台。 | Bitnami |
| ![打开 edX](media/azure-stack-marketplace-azure-items/openedx.png) | [打开 edX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 领先的发布者的 eLearning 软件。 | Bitnami |
| ![OpenFire](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | 具有 XMPP 的开源实时协作服务器。 | Bitnami |
| ![OpenProject](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | 常用的开源项目管理软件。 | Bitnami |
| ![OrangeHRM](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | 包含大量模块的 HR management system。 | Bitnami |
| ![OroCRM](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 灵活的开源 CRM 应用程序。 | Bitnami |
| ![Osclass](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 创建和管理免费的分类广告，无需技术知识。 | Bitnami |
| ![ownCloud](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | 常用的开源文件同步和共享解决方案。 | Bitnami |
| ![OXID eShop](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID eShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 受信任的开源电子商务系统。 | Bitnami |
| ![分析服务器](media/azure-stack-marketplace-azure-items/parseserver.png) | [分析服务器](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.parseserver) | Parse 是一种平台，用户可以使用它添加可缩放且功能强大的后端来启动适用于 iOS、Android、JavaScript、Windows、Unity 等的功能齐全的应用。 | Bitnami |
| ![phpBB](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | 可自定义的公告栏解决方案。 | Bitnami |
| ![phpList](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 单向电子邮件公告传递系统。 | Bitnami |
| ![Pimcore](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 强大的 engagement 管理平台（CEM/CXM）。 | Bitnami |
| ![Piwik](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | 实时 web 分析软件程序。 | Bitnami |
| ![Plone](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 免费的开源虚拟设备。 | Bitnami |
| ![Pootle](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 用于翻译项目的易于使用的 web 门户。 | Bitnami |
| ![PostgreSQL](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 高度先进的开源数据库。 | Bitnami |
| ![PostgreSQL 与复制](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgres-cluster) | PostgreSQL （Postgres）是一种开源对象关系数据库系统。 符合 ACID，它支持外键、联接、视图、触发器和存储过程。 它了解可靠性和数据完整性。 此解决方案使用多个 Vm 将数据库从主节点复制到可配置的副本数。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![PrestaShop](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | 开源电子商务网站生成器。 | Bitnami |
| ![混色 Maker 社区版](media/azure-stack-marketplace-azure-items/processmaker.png) | [混色 Maker 社区版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | 业务流程管理和工作流自动化平台。 | Bitnami |
| ![混色 Maker 企业版](media/azure-stack-marketplace-azure-items/processmaker.png) | [混色 Maker 企业版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | 开源工作流和业务流程管理软件。 | Bitnami |
| ![ProcessWire](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | 常见的 PHP5 开源 CMS。 | Bitnami |
| ![Publify](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | 基于 Ruby on Rails 的博客平台。 | Bitnami |
| ![RabbitMQ](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 有效的消息代理提供公共平台。 | Bitnami |
| ![RabbitMQ 群集](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ 群集](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq-cluster) | RabbitMQ 是一种消息传送代理，可向应用提供公共平台用于发送和接收消息，以及在收到消息之前有效地使用消息。 此解决方案使用多个 Vm 在 RabbitMQ 群集中设置多个节点，从而形成一个逻辑 broker。 **此解决方案模板还需要 Debian 8 和适用于 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Re：短线](media/azure-stack-marketplace-azure-items/redash.png) | [Re：短线](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | 开源数据可视化和协作平台。 | Bitnami |
| ![Redis](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 强大的开源键-值存储。 | Bitnami |
| ![Redis 高可用性](media/azure-stack-marketplace-azure-items/redis.png) | [Redis 高可用性](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis-cluster) | 强大的开源键-值存储。 **此解决方案模板要求 Azure Stack 集线器处于版本1807或更高版本，并且还需要 Debian 8 和 Linux 2.0 扩展的自定义脚本。** | Bitnami |
| ![Redmine](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 强大的项目管理 web 应用。 | Bitnami |
| ![Redmine + Agile](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine + Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | 已预先配置敏捷插件的项目管理应用程序。 | Bitnami |
| ![ReportServer 社区](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer 社区](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | 开源商业智能平台。 | Bitnami |
| ![ReportServer 企业](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer 企业](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | 企业商业智能平台。 | Bitnami |
| ![ResourceSpace](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | 用于改进协作的数字资产管理系统。 | Bitnami |
| ![查看板](media/azure-stack-marketplace-azure-items/reviewboard.png) | [查看板](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reviewboard) | 审查板是一种基于 web 的代码评审应用程序，它为开发人员提供不同类型的工具，轻松执行简化的代码评审。 它可以很好地从小型项目缩放到大型公司。| Bitnami |
| ![Roundcube](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | 基于浏览器的 IMAP 客户端，其功能如 MIME 支持。 | Bitnami |
| ![Ruby](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | 适用于 Ruby on Rails 的易用开发环境。 | Bitnami |
| ![SEO 面板](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO 面板](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 用于跟踪多个网站的开源 SEO 管理应用程序。 | Bitnami |
| ![Shopware](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | 开源电子商务平台。 | Bitnami |
| ![简单计算机论坛](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [简单计算机论坛](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 用于构建你自己的在线社区的简单论坛软件。 | Bitnami |
| ![Spree](media/azure-stack-marketplace-azure-items/spree.png) | [Spree](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 易于使用的电子商务平台。 | Bitnami |
| ![Subversion](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | 开源版本控制系统。 | Bitnami |
| ![SuiteCRM](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | 常见的企业级 CRM 应用程序。 | Bitnami |
| ![TensorFlow 服务](media/azure-stack-marketplace-azure-items/tensorflow.png) | [TensorFlow Serving](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tensorflowserving) | TensorFlow 服务是一种为机器学习模型提供服务的系统。 此堆栈附带了入门版3，其中包含经过训练的数据以进行图像识别，但可以对其进行扩展以提供其他模型。 | Bitnami |
| ![TestLink](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 测试管理软件促进质量保证。 | Bitnami |
| ![Tiki Wiki CMS 组件](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS 组件](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 功能齐全的 wiki 平台。 | Bitnami |
| ![小型小 RSS](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [小型小 RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 基于 web 的灵活开源新闻源和聚合器。 | Bitnami |
| ![Tomcat](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | 从 Java 社区实施规范的常用平台。 | Bitnami |
| ![跟踪](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 增强的 wiki 和问题跟踪系统。 | Bitnami |
| ![Typo3](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 完全灵活的 CMS。 | Bitnami |
| ![Weblate](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | 基于 Web 的翻译管理系统。 | Bitnami |
| ![Web 邮件 Pro PHP](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [Web 邮件 Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | 具有企业功能的 web 邮件系统。 | Bitnami |
| ![WildFly](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | 包括 Apache、WildFly、MySQL 和 Java 的应用程序服务器。 | Bitnami |
| ![WordPress](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 最受欢迎和随时可用的 CMS。 | Bitnami |
| ![WordPress 多站点](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress 多站点](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress-multisite) | WordPress 是世界上最受欢迎的博客和内容管理平台。 使用 WordPress Multisite，通过从同一服务器和界面管理多个博客和网站来节省资源。 | Bitnami |
| ![Xoops](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | 用于创建动态网站的 CMS 和 Web 门户程序。 | Bitnami |
| ![Zurmo](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | 开源 CRM 系统：Mobile、社交和 Gamified。 | Bitnami |
| ![ZooKeeper](media/azure-stack-marketplace-azure-items/zookeeper.png) | [ZooKeeper](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zookeeper) | ZooKeeper 为分布式应用程序提供了一种可靠、集中的配置数据和服务注册。 | Bitnami |
| ![Check Point CloudGuard IaaS 高可用性](media/azure-stack-marketplace-azure-items/checkpoint.png) | [CloudGuard IaaS 高可用性](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | 此解决方案部署2个成员检查点 CloudGuard IaaS 群集。 每个成员有2个网络接口。 | 检查点 |
| ![Check Point CloudGuard IaaS 安全管理](media/azure-stack-marketplace-azure-items/checkpoint.png) | [Check Point CloudGuard IaaS 安全管理](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | 此解决方案使用单个网络接口部署单一检查点安全管理服务器。 | 检查点 |
| ![Check Point CloudGuard IaaS 单一网关](media/azure-stack-marketplace-azure-items/checkpoint.png) | [Check Point CloudGuard IaaS 单一网关](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | 此解决方案部署具有2个网络接口的单一检查点 CloudGuard IaaS 安全网关。 部署完成后，应设置用户定义的路由（Udr），以便通过网关路由流量。 | 检查点 |
| ![Chef Automate](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef 自动化](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | 通过 Chef 自动执行、持续自动化平台生成、部署和管理。 **下载这两个 Chef marketplace 项**。 | Chef Software，Inc。 |
| ![Commvault](media/azure-stack-marketplace-azure-items/commvault.png) | [Commvault](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault) | 一种全面的解决方案，可用于备份和恢复、应用和 VM 迁移到 Azure Stack 中心，以及单个解决方案中 Azure Stack 中心环境的灾难恢复。 | Commvault |
| ![CloudLink SecureVM](media/azure-stack-marketplace-azure-items/cloudlink.png) | [CloudLink SecureVM](https://azuremarketplace.microsoft.com/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 Vm。 **下载所有 CloudLink SecureVM 项。** | Dell EMC |
| ![EventTracker SIEM](media/azure-stack-marketplace-azure-items/eventtracker.png) | [EventTracker SIEM](https://azuremarketplace.microsoft.com/marketplace/apps/eventtracker.eventtracker-siem)  | EventTracker SIEM 是一个全面的安全平台，它提供具有可供审核的符合性功能的高级安全工具。 | EventTracker |
| ![Exivity-混合云计费解决方案](media/azure-stack-marketplace-azure-items/exivity.png) | [Exivity-混合云计费解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/exivity.exivity-vm)  | 一种计费工具，可满足几乎任何 IT 服务交付模型的要求，无论是在本地、公有云还是混合环境中部署的。 | Exivity |
| ![f5](media/azure-stack-marketplace-azure-items/f5.png) | [f5 大 IP 虚拟版](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best)  | 高级负载平衡、GSLB、网络防火墙、DNS、WAF 和应用程序访问。 | F5 网络 |
| ![FortiGate 下一代防火墙](media/azure-stack-marketplace-azure-items/fortinetsquare.png) | [FortiGate 下一代防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-fortigate-singlevm) | 使用全面的强大安全功能套件提供完整内容和网络保护的防火墙技术。 应用程序控制、防病毒、IPS、web 筛选和 VPN，以及安全漏洞管理等高级功能，共同识别和缓解最新的复杂安全威胁。 | Fortinet |
| ![Hortonworks 数据平台（HDP）沙箱](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks 数据平台（HDP）沙箱](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | 为 Hadoop、Spark、风暴、HBase、Kafka、Hive、Ambari 提供 HDP 2.5 100% 开源平台支持。 | Hortonworks |
| ![Kaspersky](media/azure-stack-marketplace-azure-items/kaspersky.png) | [Kaspersky 等混合云安全](https://azuremarketplace.microsoft.com/marketplace/apps/kaspersky_lab.kaspersky_hybrid_cloud_security?tab=Overview) | Kaspersky 等混合云安全可实现无缝协调和自适应网络安全生态系统。 | Kaspersky 等实验室 |
| ![KEMP LoadMaster 负载均衡器 ADC 内容开关](media/azure-stack-marketplace-azure-items/kemp.png) | [KEMP LoadMaster 负载均衡器 ADC 内容开关](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | 第4-7 层应用程序传送控制器（ADC）负载均衡器、内容交换机和流量管理器。 | KEMP 技术 Inc。 |
| ![Kubernetes](media/azure-stack-marketplace-azure-items/kubernetes.png) | [Kubernetes](azure-stack-aks-engine.md) | 此解决方案使用 AKS 生成的模板来部署作为独立群集运行的 Kubernetes 群集。<br>**此解决方案模板还需要 Ubuntu Server 16.04 LTS 和适用于 Linux 2.0 的自定义脚本。**| Microsoft |
| ![Service Fabric 群集](media/azure-stack-marketplace-azure-items/servicefrabric.png) | [Service Fabric 群集](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceFabricCluster?tab=Overview) | 此解决方案部署作为虚拟机规模集上的独立群集运行的 Service Fabric。 <br>**此解决方案模板还需要下载 Windows Server 2016 Datacenter**| Microsoft |
| ![NRI mPLAT Suite-多云导体](media/azure-stack-marketplace-azure-items/nri.png) | [mPLAT Suite-多云导体](https://azuremarketplace.microsoft.com/marketplace/apps/nri.mplatmc2018-vm) | 用于监视、配置、预配、自动化和控制任何工作负荷或云的单个窗格。 | NRI |
| ![NooBaa 混合 AWS S3 兼容-社区版](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa 混合 AWS S3 兼容-社区版](https://www.noobaa.io/hybrid-cloud) | 与 S3 兼容的存储服务，跨越公共容量资源和本地容量资源。 | NooBaa |
| ![用于 Multipoint 的 NetFoundry 网关，Azure Stack 中心连接的零信任](media/azure-stack-marketplace-azure-items/netfoundry.png) | [用于 Multipoint 的 NetFoundry 网关，Azure Stack 中心连接的零信任](https://azuremarketplace.microsoft.com/marketplace/apps/netfoundryinc.centos) | 在 Azure Stack 集线器和任何位置之间通过任何网络连接进行的仅限软件的多点连接，具有业界领先的零信任安全性、VPN 吞吐量的5倍和不受限制的并行用户。 | NetFoundry |
| ![Palo Alto VM 系列下一代防火墙](media/azure-stack-marketplace-azure-items/paloalto.png) | [Palo Alto VM 系列下一代防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/paloaltonetworks.vmseries-ngfw) | VM 系列下一代防火墙使客户能够安全地将其应用程序和数据迁移到 Azure Stack 中心，并通过应用允许列表和威胁防护策略保护他们免受未知和未知的威胁。 **此映像需要部署模板;请参阅此[文章](https://www.paloaltonetworks.com/documentation/81/virtualization/virtualization/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewalls-on-azure-stack)了解重要信息。**| Palo Alto Networks，Inc。 |
| ![PT 应用程序防火墙](media/azure-stack-marketplace-azure-items/pt.png) | [PT 应用程序防火墙](https://azuremarketplace.microsoft.com/marketplace/apps/ptsecurity.ptaf) | PT 应用程序防火墙检测已知 & 未知漏洞，并阻止对 web 应用的攻击。 **下载这两个 PT Marketplace 项。** | 正面技术 |
| ![Puppet 企业](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet 企业](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | Puppet Enterprise 使你能够自动完成 Azure Stack 中心基础结构的整个生命周期。 **下载这两个 Puppet Marketplace 项。** | Puppet |
| ![寻找快速恢复](media/azure-stack-marketplace-azure-items/quest.png) | [寻找快速恢复核心](https://azuremarketplace.microsoft.com/marketplace/apps/quest.rapid-recovery-core-vm) | 快速恢复高级数据保护在一个易于使用的软件解决方案中统一了备份、复制和恢复。 | 寻找软件 |
| ![SIOS DataKeeper 群集版本](media/azure-stack-marketplace-azure-items/sioslogo.png) | [SIOS DataKeeper 群集版本](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) | SIOS DataKeeper 在 Azure Stack 集线器中提供高可用性（HA）和灾难恢复（DR）。 只需在 Azure Stack 中心部署中将 SIOS DataKeeper software 作为成分添加到 Windows Server 故障转移群集（WSFC）环境中，即可无需共享存储。 | SIOS 技术公司。 |
| ![SUSE Manager 3.1 代理（BYOS）](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.1 代理（BYOS）](https://azuremarketplace.microsoft.com/marketplace/apps/suse.suse-manager-proxy-byos) | 同类最佳开源基础结构管理。 | SUSE |
| ![Teradici 云访问软件](media/azure-stack-marketplace-azure-items/teradici.png) | [Teradici 云访问软件](https://azuremarketplace.microsoft.com/marketplace/apps/teradici.teradici_cas_azure_stack) | 云访问软件由 PCoIP®技术提供支持，可 Azure Stack 在任何位置向任意设备提供远程桌面和工作站。 整合数据存储、增强协作、保护数据、简化桌面管理等。 | Teradici |
| ![CipherTrust 云密钥管理器](media/azure-stack-marketplace-azure-items/cckm.png) | [CipherTrust 云密钥管理器](https://azuremarketplace.microsoft.com/marketplace/apps/thales-vormetric.ciphertrust-ckm) | 通过利用 Microsoft Azure 和其他云提供商创建自己的密钥（BYOK） Api，CipherTrust 云密钥管理器通过提供对加密密钥的多云生命周期控制（集中管理和可见性。 | Thales eSecurity |
| ![Veeam 备份 & 复制](media/azure-stack-marketplace-azure-items/veeam.png) | [Veeam 备份 & 复制](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=Overview) | Veeam®备份 & 复制™有助于企业针对所有工作负荷（虚拟、物理和基于云）实现全面的数据保护。 使用单个控制台，可以实现快速、灵活且可靠的所有应用和数据的备份、恢复和复制。 | Veeam 软件 |
| ![ZeroDown Software BCaaS](media/azure-stack-marketplace-azure-items/zerodown.png) | [将软件业务连续性 ZeroDown 服务](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.bcaasforazure) | ZeroDown®软件技术使企业能够通过业务连续性（BCaaS）服务（）对其公司数据进行持续访问，™体系结构、保护应用和交易，如果发生的网络中断通常会使版. | ZeroDown 软件 |
