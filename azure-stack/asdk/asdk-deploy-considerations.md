---
title: ASDK 要求和注意事项
description: 了解 Azure Stack 开发工具包 (ASDK) 的硬件、软件和环境要求。
author: justinha
ms.topic: article
ms.date: 05/13/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 05/13/2019
ms.openlocfilehash: 889180858826685150fadb7ad3bef0ffb789b8ec
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "79294414"
---
# <a name="asdk-requirements-and-considerations"></a>ASDK 要求和注意事项

在部署 Azure Stack 开发工具包 (ASDK) 之前，请确保 ASDK 主机满足本文中所述的要求。

## <a name="hardware"></a>硬件

| 组件 | 最小值 | 建议 |
| --- | --- | --- |
| 磁盘驱动器：操作系统 |1 个操作系统磁盘，至少 200 GB 用于系统分区（SSD 或 HDD）。 |1 块 OS 磁盘，至少 200 GB 用于系统分区（SSD 或 HDD）。 |
| 磁盘驱动器：常规开发工具包数据<sup>*</sup>  |4 块磁盘。 每个磁盘提供至少 240 GB 的容量（SSD 或 HDD）。 将使用所有可用的磁盘。 |4 块磁盘。 每个磁盘提供至少 400 GB 的容量（SSD 或 HDD）。 将使用所有可用的磁盘。 |
| 计算：CPU |双插槽：16 个物理核心（总计）。 |双插槽：20 个物理核心（总计）。 |
| 计算：内存 |192-GB RAM。 |256-GB RAM。 |
| 计算：BIOS |Hyper-V 已启用（提供 SLAT 支持）。 |Hyper-V 已启用（提供 SLAT 支持）。 |
| 网络：NIC |Windows Server 2012 R2 认知。 不要求使用专用功能。 | Windows Server 2012 R2 认知。 不要求使用专用功能。 |
| 硬件徽标认证 |[针对 Windows Server 2012 R2 的认证](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)。 |[Windows Server 2016 认证](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)。 |

<sup>*</sup>如果计划从 Azure 添加多个[市场项](../operator/azure-stack-create-and-publish-marketplace-item.md)，则需要的容量比这个建议的容量要大。

### <a name="hardware-notes"></a>硬件说明

**数据磁盘驱动器配置：** 所有数据驱动器的类型都必须相同（全部为 SAS、全部为 SATA 或全部为 NVMe）且容量也必须相同。 如果使用 SAS 磁盘驱动器，则这些磁盘驱动器必须通过单个路径来附加（不提供 MPIO 多路径支持）。

**HBA 配置选项**

* （首选）简单 HBA。
* RAID HBA - 适配器必须在“直通”模式下配置。
* RAID HBA - 磁盘应配置为单磁盘，RAID-0。

**支持的总线和介质类型组合**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD（如果介质类型为“未指定/未知”<sup>*</sup>）
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> 没有直通功能的 RAID 控制器无法识别此介质类型。 此类控制器会将 HDD 和 SSD 都标记为“未指定”。 在这种情况下，SSD 将用作持久存储而不是缓存设备。 因此，可以在这些 SSD 上部署 ASDK。

**示例 HBA**：LSI 9207-8i、LSI-9300-8i 或 LSI-9265-8i（在直通模式下）。

提供了示例 OEM 配置。

### <a name="storage-resiliency-for-the-asdk"></a>ASDK 的存储复原

ASDK 是一种单节点系统，设计用于验证 Azure Stack 集成系统的生产冗余。 不过，你可以对 HDD 和 SSD 驱动器进行优化组合，提高 ASDK 的基础存储冗余的级别。 可以部署类似于 RAID1 的双向镜像配置，而不是类似于 RAID0 的简单复原配置。 使用足够的容量、类型和驱动器数进行基础的存储空间直通配置。

若要将双向镜像配置用于存储复原，必须符合以下条件：

- 需要系统中的 HDD 容量大于 2 TB。
- 如果 ASDK 中没有 SSD，则需要至少 8 个 HDD 才能完成双向镜像配置。
- 如果 ASDK 中有 SSD 和 HDD，则需要至少 5 个 HDD。 不过，建议使用 6 个 HHD。 如果使用 6 个 HDD，则建议在系统中使用至少 3 个相应的 SSD，让一个缓存磁盘 (SSD) 为两个容量磁盘 (HDD) 服务。

双向镜像配置示例：

- 8 个 HDD
- 3 个 SSD/6 个 HDD
- 4 个 SSD/8 个 HDD

## <a name="operating-system"></a>操作系统
|  | **要求** |
| --- | --- |
| **OS 版本** |Windows Server 2016 或更高版本。 在部署开始之前，操作系统版本不是很重要，因为你会将主计算机启动到 VHD 中，而该 VHD 已包括在 Azure Stack 安装中。 操作系统和所有必需的修补程序已集成到映像中。 请勿使用任何密钥来激活在 ASDK 中使用的任何 Windows Server 实例。 |

> [!TIP]
> 安装操作系统后，可以使用[适用于 Azure Stack 的部署检查器](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)来确认硬件是否满足所有要求。

## <a name="account-requirements"></a>帐户要求
通常情况下，你将 ASDK 与 internet 连接一起部署，你可以在其中连接到 Microsoft Azure。 在这种情况下，必须先配置 Azure Active Directory (Azure AD) 帐户，然后才能部署 ASDK。

如果环境未连接到 Internet，或者不希望使用 Azure AD，则可使用 Active Directory 联合身份验证服务 (AD FS) 来部署 Azure Stack。 ASDK 包含自己的 AD FS 和 Active Directory 域服务实例。 如果使用此选项进行部署，则不需提前设置帐户。

> [!NOTE]
> 如果使用 AD FS 选项进行部署，则必须重新部署 Azure Stack，以便切换到 Azure AD。

### <a name="azure-active-directory-accounts"></a>Azure Active Directory 帐户
若要使用 Azure AD 帐户来部署 Azure Stack，必须先准备 Azure AD 帐户，然后再运行 PowerShell 部署脚本。 此帐户成为 Azure AD 租户的全局管理员。 对于所有与 Azure AD 和图形 API 交互的 Azure Stack 服务，可以使用它来预配和委托应用和服务主体。 也可将它用作默认提供商订阅（可以稍后更改）的所有者。 可以使用此帐户登录到 Azure Stack 系统的管理员门户。

1. 创建一个 Azure AD 帐户，该帐户是至少一个 Azure AD 的目录管理员。 如果已经有一个这样的帐户，则可以使用该帐户。 否则，你可以免费创建一个[https://azure.microsoft.com/free/](https://azure.microsoft.com/free/) （在中国，请参阅<https://go.microsoft.com/fwlink/?LinkID=717821> ）。 如果打算以后[将 Azure Stack 注册到 Azure](asdk-register.md)，则还必须在这个新创建的帐户中有一个订阅。
   
    保存这些以服务管理员身份使用的凭据。此帐户可以配置和管理资源云、用户帐户、租户计划、配额和定价。 在门户中，该帐户可以创建网站云和 VM 私有云、创建计划，以及管理用户订阅。
1. 在 Azure AD 中至少创建一个测试用户帐户，以便以租户身份登录到 ASDK。
   
   | **Azure Active Directory 帐户** | **支持？** |
   | --- | --- |
   | 具有有效全局 Azure 订阅的工作或学校帐户 |是 |
   | 具有有效全局 Azure 订阅的 Microsoft 帐户 |是 |
   | 使用有效的中国 Azure 订阅的工作或学校帐户 |是 |
   | 使用有效的美国政府版 Azure 订阅的工作或学校帐户 |是 |

部署后，不需要 Azure AD 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 此类操作的示例包括资源提供程序安装程序脚本或需要授予权限的新功能。 可以暂时恢复帐户的全局管理员权限，也可以使用*默认提供程序订阅*所有者的单独全局管理员帐户。

## <a name="network"></a>Network (网络)
### <a name="switch"></a>开关
交换机上的一个可用于 ASDK 计算机的端口。  

ASDK 计算机支持连接到交换机访问端口或 Trunk 端口。 交换机上不要求使用专用功能。 若要使用 Trunk 端口，或者需要配置 VLAN ID，则需以部署参数的形式提供 VLAN ID。

### <a name="subnet"></a>子网
请勿将 ASDK 计算机连接到以下子网：

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

这些子网是为 ASDK 环境中的内部网络保留的。

### <a name="ipv4ipv6"></a>IPv4/IPv6
仅支持 IPv4。 无法创建 IPv6 网络。

### <a name="dhcp"></a>DHCP
确保可以在 NIC 连接到的网络上使用 DHCP 服务器。 如果 DHCP 不可用，则除了主机使用的静态 IPv4 网络，还必须准备另一个此类网络。 必须提供该 IP 地址和网关作为部署参数。

### <a name="internet-access"></a>Internet 访问权限
Azure Stack 需要访问 Internet，可以直接访问，也可以通过透明代理进行访问。 Azure Stack 不支持通过配置 Web 代理来启用 Internet 访问。 主机 IP 和分配到 AzS-BGPNAT01 的新 IP（通过 DHCP 或静态 IP 的方式进行分配）必须能够访问 Internet。 在 graph.windows.net 和 login.microsoftonline.com 域下使用端口80和443。


## <a name="next-steps"></a>后续步骤

- [下载 ASDK 部署包](asdk-download.md)。
- 若要详细了解存储空间直通，请参阅[存储空间直通概述](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)。

