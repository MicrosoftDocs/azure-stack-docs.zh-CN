---
title: Azure Stack 开发工具包 (ASDK) 部署先决条件 | Microsoft Docs
description: 查看 Azure Stack 开发工具包 (ASDK) 的环境和硬件要求。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 05/13/2019
ms.openlocfilehash: 9cb349ec19edd493ca994b406b9311fe27bed242
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712238"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Azure Stack 部署规划注意事项

在部署 Azure Stack 开发工具包 (ASDK) 之前，请确保开发工具包主机满足本文中所述的要求。

## <a name="hardware"></a>硬件

| 组件 | 最小值 | 建议 |
| --- | --- | --- |
| 磁盘驱动器：操作系统 |1 个操作系统磁盘，至少 200 GB 用于系统分区（SSD 或 HDD） |1 块 OS 磁盘，至少 200 GB 用于系统分区（SSD 或 HDD） |
| 磁盘驱动器：常规开发工具包数据<sup>*</sup>  |4 块磁盘。 每个磁盘提供至少 240 GB 的容量（SSD 或 HDD）。 将使用所有可用的磁盘。 |4 块磁盘。 每个磁盘提供至少 400 GB 的容量（SSD 或 HDD）。 将使用所有可用的磁盘。 |
| 计算：CPU |双插槽：16 个物理核心（总计） |双插槽：20 个物理核心（总计） |
| 计算：内存 |192-GB RAM |256-GB RAM |
| 计算：BIOS |Hyper-V 已启用（提供 SLAT 支持） |Hyper-V 已启用（提供 SLAT 支持） |
| 网络：NIC |Windows Server 2012 R2 认知。 不要求使用专用功能 |Windows Server 2012 R2 认知。 不要求使用专用功能 |
| 硬件徽标认证 |[针对 Windows Server 2012 R2 的认证](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2016 认证](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup>如果计划从 Azure 添加多个[市场项](../operator/azure-stack-create-and-publish-marketplace-item.md)，则需要的容量比这个建议的容量要大。

### <a name="hardware-notes"></a>硬件说明

**数据磁盘驱动器配置：** 所有数据驱动器的类型都必须相同（全部为 SAS、全部为 SATA 或全部为 NVMe）且容量也必须相同。 如果使用 SAS 磁盘驱动器，则这些磁盘驱动器必须通过单个路径来附加（不提供 MPIO 多路径支持）。

**HBA 配置选项**

* （首选）简单 HBA
* RAID HBA-适配器必须在"直通"模式下配置
* RAID HBA - 磁盘应配置为单磁盘，RAID-0

**支持的总线和介质类型组合**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD（如果介质类型为“未指定/未知”<sup>*</sup>）
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> 没有直通功能的 RAID 控制器无法识别此介质类型。 此类控制器会将 HDD 和 SSD 都标记为“未指定”。 在这种情况下，SSD 将用作持久存储而不是缓存设备。 因此，可以在这些 SSD 上部署开发工具包。

**示例 HBA**：LSI 9207-8i、LSI-9300-8i 或 LSI-9265-8i（在直通模式下）

提供了示例 OEM 配置。

### <a name="storage-resiliency-for-the-asdk"></a>对于 asdk 来说存储复原能力

为单个节点系统，ASDK 的用途不是验证生产冗余的 Azure Stack 集成系统。 但是，可以提高 ASDK 通过 HDD 和 SSD 驱动器的最佳组合的基础存储冗余的级别。 你可以部署一个双向镜像配置的类似于 RAID1，而不是一个简单的复原能力配置，这类似于 RAID0。 基础存储空间直通配置为使用足够的容量、 类型和驱动器数目。

要用于存储复原能力的双向镜像配置：

- 在系统中的大于 2 千吉字节 HDD 容量。
- 如果在 ASDK 中，不包含 Ssd，则需要至少为八个 Hdd 为双向镜像配置。
- 如果 Ssd 在 ASDK 中，以及 Hdd，您将需要至少五个 Hdd。 但是，建议使用六个 Hhd。 为六个 Hdd，它还建议在系统中具有相应至少三个 Ssd，以便您具有一个缓存磁盘 (SSD) 提供两个容量驱动器 (HDD)。

双向镜像的示例配置：

- 八个 Hdd
- 三个 SSD / 六个 HDD
- 四个 SSD / 八个 HDD

## <a name="operating-system"></a>操作系统
|  | **要求** |
| --- | --- |
| **OS 版本** |Windows Server 2016 或更高版本。 操作系统版本不是严重之前启动部署，因为将主机计算机启动到 Azure Stack 安装中包括的 VHD。 操作系统和所有必需的修补程序已集成到映像中。 不使用任何密钥来激活在开发工具包中使用任何 Windows Server 实例。 |

> [!TIP]
> 安装操作系统后，可以使用[适用于 Azure Stack 的部署检查器](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)来确认硬件是否满足所有要求。

## <a name="account-requirements"></a>帐户要求
通常情况下，在具有 internet 连接，你可以连接到 Microsoft Azure 部署开发工具包。 在这种情况下，必须先配置 Azure Active Directory (Azure AD) 帐户，然后才能部署开发工具包。

如果环境未连接到 Internet，或者不希望使用 Azure AD，则可使用 Active Directory 联合身份验证服务 (AD FS) 来部署 Azure Stack。 开发工具包包含自己的 AD FS 和 Active Directory 域服务实例。 如果使用此选项进行部署，则不需提前设置帐户。

> [!NOTE]
> 如果使用 AD FS 选项进行部署，则必须重新部署 Azure Stack，以便切换到 Azure AD。

### <a name="azure-active-directory-accounts"></a>Azure Active Directory 帐户
若要使用 Azure AD 帐户来部署 Azure Stack，必须先准备 Azure AD 帐户，然后再运行 PowerShell 部署脚本。 此帐户成为 Azure AD 租户的全局管理员。 对于所有与 Azure Active Directory 和图形 API 交互的 Azure Stack 服务，可以使用它来预配和委托应用程序和服务主体。 也可将它用作默认提供商订阅（可以稍后更改）的所有者。 您可以使用此帐户登录到 Azure Stack 系统的管理员门户。

1. 创建一个 Azure AD 帐户，该帐户是至少一个 Azure AD 的目录管理员。 如果已经有一个这样的帐户，则可以使用该帐户。 否则，可以通过 [https://azure.microsoft.com/free/](https://azure.microsoft.com/free/)（中国用户请访问 <https://go.microsoft.com/fwlink/?LinkID=717821>）创建一个免费帐户。 如果打算以后[将 Azure Stack 注册到 Azure](asdk-register.md)，则还必须在这个新创建的帐户中有一个订阅。
   
    保存这些以服务管理员身份使用的凭据。 此帐户可以配置和管理资源云、用户帐户、租户计划、配额和定价。 在门户中，该帐户可以创建网站云和虚拟机专用云、创建计划，以及管理用户订阅。
1. 在 Azure AD 中至少创建一个测试用户帐户，以便以租户身份登录到开发工具包。
   
   | **Azure Active Directory 帐户** | **支持？** |
   | --- | --- |
   | 具有有效的全局 Azure 订阅的工作或学校帐户 |“是” |
   | 具有有效的全局 Azure 订阅的 Microsoft 帐户 |“是” |
   | 具有有效的中国区 Azure 订阅的工作或学校帐户 |“是” |
   | 具有有效的美国政府版 Azure 订阅的工作或学校帐户 |“是” |

部署后，不需要 Azure Active Directory 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 例如，资源提供程序安装程序脚本或需要授予权限的新功能。 可以临时复原帐户的全局管理员权限，也可以使用单独的全局管理员帐户（该帐户应是*默认提供程序订阅*的所有者）。

## <a name="network"></a>网络
### <a name="switch"></a>切换
交换机上的一个可用于开发工具包计算机的端口。  

开发工具包计算机支持连接到交换机访问端口或 Trunk 端口。 交换机上不要求使用专用功能。 若要使用 Trunk 端口，或者需要配置 VLAN ID，则需以部署参数的形式提供 VLAN ID。

### <a name="subnet"></a>子网
请勿将开发工具包计算机连接到以下子网：

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

这些子网是为开发工具包环境中的内部网络保留的。

### <a name="ipv4ipv6"></a>IPv4/IPv6
仅支持 IPv4。 无法创建 IPv6 网络。

### <a name="dhcp"></a>DHCP
确保可以在 NIC 连接到的网络上使用 DHCP 服务器。 如果 DHCP 不可用，则除了主机使用的静态 IPv4 网络，还必须准备另一个此类网络。 必须提供该 IP 地址和网关作为部署参数。

### <a name="internet-access"></a>Internet 访问权限
Azure Stack 需要访问 Internet，可以直接访问，也可以通过透明代理进行访问。 Azure Stack 不支持通过配置 Web 代理来启用 Internet 访问。 主机 IP 和分配到 AzS-BGPNAT01 的新 IP（通过 DHCP 或静态 IP 的方式进行分配）必须能够访问 Internet。 Graph.windows.net 和 login.microsoftonline.com 域下将使用端口 80 和 443。


## <a name="next-steps"></a>后续步骤

- [下载 ASDK 部署包](asdk-download.md)
- 若要了解有关存储空间直通的详细信息，请参阅[存储空间直通概述](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)。

