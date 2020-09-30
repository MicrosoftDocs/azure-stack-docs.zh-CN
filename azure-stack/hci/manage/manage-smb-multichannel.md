---
title: 管理 SMB 多通道
description: 使用本主题可使用 SMB 多通道提高吞吐量和网络容错能力。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: b7dc0c8050bc9d813115d2c3ebd7716b87bd89fa
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585888"
---
# <a name="manage-smb-multichannel"></a>管理 SMB 多通道

> 适用于： Azure Stack HCI，版本 20H2;Windows Server 2019;Windows Server 2016;Windows Server 2012 R2;Windows Server 2012;Windows 10

SMB 多通道是服务器消息块的一部分 (SMB) 3.0 协议，可提高网络性能和文件服务器的可用性。

SMB 多通道允许文件服务器同时使用多个网络连接。 当 SMB 3.0 客户端和 SMB 3.0 服务器之间有多个路径可用时，它可以简化网络带宽和网络容错的聚合。 这使服务器应用程序能够充分利用所有可用的网络带宽，使其能够更灵活地应对网络故障。

SMB 多通道提供以下功能：

- **提高了吞吐量。** 文件服务器可以通过使用高速网络适配器或多个网络适配器的多个连接，同时传输更多的数据。

- **网络容错。** 当客户端同时使用多个网络连接时，即使网络连接中断，客户端也可以继续而不会中断。

- **自动配置。** SMB 多通道自动发现多个可用的网络路径，并根据需要动态添加连接。

## <a name="requirements-for-smb-multichannel"></a>SMB 多通道的要求

由于默认情况下启用 SMB 多通道，因此你不必安装其他角色、角色服务或功能。 标识配置后，SMB 客户端会自动检测并使用多个网络连接。 但是，SMB 多通道具有以下要求：

- 至少两台运行 Azure Stack HCI、Windows Server 或 Windows 10 操作系统的计算机。

- 至少以下配置之一：

    - 多个网络适配器

    - 支持接收方缩放 (RSS) 的一个或多个网络适配器

    - 多个组网络适配器 (参阅 [NIC 组合](#nic-teaming)) 

    - 支持 (RDMA 远程直接内存访问的一个或多个网络适配器) 

## <a name="configure-smb-multichannel"></a>配置 SMB 多通道

本部分介绍使用网络适配器数组部署 SMB 多通道的一些配置选项。 这些配置仅包括配置。 本主题中未包含其他许多可能的配置。

### <a name="single-rss-capable-network-adapter"></a>支持 RSS 的单个网络适配器

在此典型配置中，SMB 客户端和 SMB 服务器是使用单个10千兆位以太网 (10 GbE) 网络适配器配置的。 在不使用 SMB 多通道部署 SMB 的情况下，如果只建立了一个 SMB 会话，SMB 将创建一个 TCP/IP 连接。 仅使用单个 CPU 内核时，此配置在本质上会导致拥塞，尤其是在执行许多小型 i/o 操作时。 因此，性能瓶颈的可能性很高。

大多数当前网络适配器提供一项称为接收方缩放 (RSS) 的功能，该功能允许多个连接在多个 CPU 内核之间自动传播。 但是，如果使用单个连接，RSS 将不会有帮助。 将 SMB 多通道与支持 RSS 的网络适配器一起使用时，SMB 将为该特定会话创建多个 TCP/IP 连接。 如果需要很多小型 i/o 操作，则此配置可避免单个 CPU 内核出现潜在瓶颈。

### <a name="multiple-network-adapters"></a>多个网络适配器

在此配置中，SMB 客户端和 SMB 服务器是使用多个 10 GbE 网络适配器配置的。 在不使用 SMB 多通道部署 SMB 的情况下，如果只建立了一个 SMB 会话，SMB 将使用其中一个可用的网络适配器来创建单个 TCP/IP 连接。 在这种情况下，不可能聚合多个网络适配器的带宽;例如，如果使用两个 1 GbE 网络适配器，则不能在 (Gbps) 下实现2千兆字节/秒。 如果所选网络适配器已断开连接或已禁用，也可能出现故障。

当使用 SMB 多通道部署 SMB 时，如果网络适配器支持 RSS 功能，SMB 将为单一会话创建多个 TCP/IP 连接，每个接口至少有一个或多个连接。 此配置使 SMB 能够使用可用的组合网络适配器带宽，并且如果网络适配器发生故障，SMB 客户端就可以继续进行而不中断。

### <a name="nic-teaming"></a>NIC 组合

Azure Stack HCI 和 Windows Server 支持通过使用称为 NIC 组合的功能将多个网络适配器合并到一个网络适配器中。 尽管在不使用 SMB 多通道的情况下部署 SMB 时，团队始终会提供容错，但 SMB 每个团队仅创建一个 TCP/IP 连接。 此配置会导致占用的 CPU 内核数和团队带宽的最大使用限制。

当使用 SMB 多通道部署 SMB 时，SMB 为单个会话创建多个 TCP/IP 连接，以在 CPU 核心之间实现更好的平衡，并更好地利用可用带宽。 NIC 组合将继续提供故障转移功能，其工作速度要快于使用 SMB 多通道。 我们还建议采用 NIC 组合，因为它向不依赖于 SMB 的其他工作负荷提供故障转移功能，因为这些工作负荷无法从 SMB 多通道的故障转移功能中获益。

如果使用一组专用的网络适配器来实现存储空间直通的流量，有时使用 Azure Stack HCI 完成，组合这些存储网络适配器是完全可选的，它不会带来任何明显的好处或缺点。

>[!IMPORTANT]
>在 Windows Server 2012 R2 及更早版本中，如果您打算使用网络适配器的 RDMA 功能，请不要使用 NIC 组合。 在这些操作系统上，支持 RDMA 的网络适配器组始终报告为非 RDMA 功能，因为组合会禁用网络适配器的 RDMA 功能。

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>单个或多个支持 RDMA 的网络适配器

SMB 多通道检测网络适配器的 RDMA 功能，这将启用通过 RDMA 称为 SMB 直通的 SMB 直接功能。 如果没有 SMB 多通道，则 SMB 会将常规 TCP/IP 连接与支持 RDMA 功能的网络适配器结合使用，其中所有网络适配器都提供与新 RDMA 堆栈并行存在的 TCP/IP 堆栈。

当使用 SMB 多通道部署 SMB 时，SMB 将检测网络适配器的 RDMA 功能，并为该单一会话创建多条 RDMA 连接，每个接口有两个 RDMA 连接。 此配置使 SMB 能够利用支持 RDMA 的网络适配器提供的高吞吐量、低延迟和低 CPU 使用率。 使用多个 RDMA 接口时，它还可以提供容错功能。

>[!IMPORTANT]
>创建 RDMA 连接后，将不再使用原始协议协商的 TCP/IP 连接。 但是，在不同的 RDMA 连接失败的情况下，将保留此连接。

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>SMB 多通道、支持 RDMA 的网络适配器和 NIC 组合兼容性

下表汇总了在将 SMB 多通道、RDMA (SMB 直通) 和 NIC 组合结合使用时可用的不同功能。

| Configuration                                           | 吞吐量 | SMB 的容错能力 | 非 SMB 容错 | 降低 CPU 利用率 |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| 单个网络适配器 (没有 RSS)                          | *          |                         |                             |                       |
| 多个网络适配器不 (RSS)                       | **         | *                       |                             |                       |
| 多个网络适配器不 (具有 NIC 组合的 RSS)      | **         | **                      | *                           |                       |
| 带有 RSS 的单个网络适配器                         | *          |                         |                             |                       |
| 多个带 RSS 的网络适配器                      | **         | *                       |                             |                       |
| 具有 RSS 和 NIC 组合的多个网络适配器      | **         | **                      | *                           |                       |
| 支持单一 RDMA 的网络适配器                     | *          |                         |                             | *                     |
| 多个支持 RDMA 的网络适配器                  | ***        | *                       |                             | *                     |
| 具有 NIC 组合的多个支持 RDMA 的网络适配器 | ***        | **                      | *                           | *                     |

如果运行的是 Windows Server 2016 或更高版本，理想的解决方案是使用多个支持 RDMA 功能的网络适配器并将 NIC 组合与 SMB 多通道组合在一起。 这种组合提供了最佳吞吐量，为使用 SMB 和其他协议的应用程序提供了容错能力，并且对 CPU 的影响最小。

如上所述，在 Windows Server 2012 R2 或更早版本上使用支持 RDMA 功能的网络适配器时，NIC 组合并不是一个不错的选择，因为它禁用网络适配器的 RDMA 功能。

### <a name="example-configurations-without-smb-multichannel"></a>不包含 SMB 多通道的示例配置

如果计划使用不带 RSS 的单个网络适配器，则无法从多个网络连接中获益，因此不使用 SMB 多通道。 此外，如果你计划使用不同速度的网络适配器，SMB 多通道将自动选择最快的网络适配器。 这是因为同一类型的网络适配器 (如 RDMA、RSS 或) 并且具有相同的速度，SMB 多通道同时使用。 较慢的网络适配器处于空闲状态。

## <a name="disable-smb-multichannel"></a>禁用 SMB 多通道
通常，不需要禁用 SMB 多通道。 但是，如果要禁用 SMB 多通道（例如在测试环境中），请使用以下 Windows PowerShell 过程。

首先，通过打开 PowerShell 会话连接到其中一个服务器：

```PowerShell
Enter-PSSession <server-name>
```

若要在服务器端禁用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

若要在客户端上禁用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>禁用客户端或服务器上的 SMB 多通道会阻止两个系统使用它。

## <a name="re-enable-smb-multichannel"></a>重新启用 SMB 多通道

如果已禁用 SMB 多通道并想要重新启用它，请使用以下过程。

若要在服务器端重新启用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

若要在客户端重新启用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>你必须在客户端和服务器上重新启用 SMB 多通道，才能再次开始使用它。

## <a name="test-smb-multichannel"></a>测试 SMB 多通道

本部分介绍了一些用于测试 SMB 多通道的方案，其中包括将文件副本与和不包含 SMB 多通道进行比较，并有意导致网络适配器在文件复制操作过程中失败。

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>比较具有和不包含 SMB 多通道的文件副本

若要测量 SMB 多通道提供的增加的吞吐量，请执行以下测试。 在每次测试前重启服务器和客户端，以确保它们在相似条件下运行。

1. 使用前面所述的配置之一配置 SMB 多通道。

1. 测量使用 SMB 多通道执行长时间运行的文件复制操作所需的时间。

1. 禁用 SMB 多通道。 有关说明，请参阅 [禁用 SMB 多通道](#disable-smb-multichannel)。

1. 度量执行不使用 SMB 多通道的相同文件副本所需的时间量。

1. 重新启用 SMB 多通道。 有关说明，请参阅 [重新启用 SMB 多通道](#re-enable-smb-multichannel)。

1. 比较两个结果。

>[!IMPORTANT]
>若要避免缓存对性能的影响，请先复制超过可用内存大小的大量数据。 然后再次执行复制操作，并使用第一个复制操作作为预热。 仅时间为第二个复制操作。

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>使用 SMB 多通道在文件复制过程中导致其中一个网络适配器出现故障

若要确认 SMB 多通道的故障转移功能：

1. 请确保 SMB 多通道在多个网络适配器配置中运行。

1. 执行长时间运行的文件复制操作。

1. 当文件复制操作正在运行时，通过断开一条电缆或禁用其中一个网络适配器来模拟其中一个网络路径的故障。

1. 确认文件复制继续使用剩余的网络适配器，并在没有任何文件复制错误的情况下运行。

请确保没有其他工作负荷使用断开连接的网络连接路径。 这种预防措施避免了不利用 SMB 多通道的工作负荷中可能出现的故障。

## <a name="verify-that-smb-multichannel-works"></a>验证 SMB 多通道工作

使用以下过程来验证 SMB 多通道是否正常工作。

1. 若要验证网络适配器配置是否正确，请在 SMB 服务器和 SMB 客户端上的 Windows PowerShell 中键入以下各项。

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. 若要验证 SMB 多通道是否已启用，请确认 SMB 正确标识网络适配器，并正确确定网络适配器的 RSS 和 RDMA 功能，请执行以下操作：

   在 SMB 客户端上，在 Windows PowerShell 中键入以下内容：

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   在 SMB 服务器上，在 Windows PowerShell 中键入以下内容：

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. 在 SMB 客户端上，执行长时间运行的文件复制操作，该操作将创建与 SMB 服务器的正在进行的会话。 当复制操作正在运行时，请在 Windows PowerShell 中键入以下各项，以验证连接是否使用正确版本的 SMB 以及 SMB 多通道是否正常工作。

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [使用 Windows Server 中的 SMB 3 协议的文件共享概述](/windows-server/storage/file-server/file-server-smb-overview)
- [简化的 SMB 多通道和多 NIC 群集网络](/windows-server/failover-clustering/smb-multichannel)
- [SMB 直通](/windows-server/storage/file-server/smb-direct)
