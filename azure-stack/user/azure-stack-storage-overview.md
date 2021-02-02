---
title: Azure Stack Hub 存储简介
description: 了解 Azure Stack Hub 存储服务。
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 3c02ef69ce2359c050e381d4dec027c28f157765
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247551"
---
# <a name="introduction-to-azure-stack-hub-storage"></a>Azure Stack Hub 存储简介

## <a name="overview"></a>概述

Azure Stack Hub 存储是一组与 Azure 存储提供的服务一致的云存储服务。 这些服务包括 Blob、表和队列。

## <a name="azure-stack-hub-storage-services"></a>Azure Stack Hub 存储服务

Azure Stack Hub 存储提供以下三个服务：

- **Blob 存储**

    Blob 存储用于存储非结构化对象数据。 Blob 可以是任何类型的文本或二进制数据，例如文档、媒体文件或应用安装程序。

- **表存储**

    表存储可存储结构化数据集。 表存储是一个 NoSQL“键-属性”数据存储，可以用于实现快速开发以及快速访问大量数据。

- **队列存储**

    队列存储为云服务的各个组件之间的工作流处理和通信提供可靠的消息传送。

Azure Stack Hub 存储帐户是可让你访问 Azure Stack Hub 存储的安全帐户。 存储帐户为存储资源提供唯一的命名空间。 下图显示了存储帐户中 Azure Stack Hub 存储资源之间的关系：

![Azure Stack Hub 存储概述](media/azure-stack-storage-overview/azurestackstorageoverview.svg)

### <a name="blob-storage"></a>Blob 存储

对于有大量非结构化对象数据要存储在云中的用户，Blob 存储提供有效且可缩放的解决方案。 使用 Blob 存储来存储如下内容：

- 文档
- 社交数据，例如照片、视频、音乐和博客
- 文件、计算机、数据库和设备的备份
- Web 应用的图像和文本
- 云应用的配置数据
- 大数据，例如日志和其他大型数据集

每个 Blob 都组织到一个容器中。 容器还提供了一种有用的方式来向对象组分配安全策略。 一个存储帐户可以包含任意数目的容器，一个容器可以包含任意数目的 Blob（最高可达存储帐户的限制）。

Blob 存储提供三种类型的 blob：

- **块 blob**

    块 Blob 进行了相应的优化来流化和存储云对象， 是用于存储文档、媒体文件、备份以及其他类似文件的不错选择。

- **追加 blob**

    追加 Blob 类似于块 Blob，但针对追加追加操作进行了优化。 追加 Blob 仅可以通过将新的块添加到末尾来进行更新。 对于需要新数据只能写入到 Blob 结尾的情况，例如日志记录，追加 Blob 是一个不错的选择 。

- **页 blob**

    页 Blob 已针对表示 IaaS 磁盘和支持随机写入进行优化，且大小可以高达 1 TB。 Azure Stack Hub 虚拟机连接的 IaaS 磁盘是以页 blob 方式存储的 VHD。

### <a name="table-storage"></a>表存储

与前几代的必需软件相比，现代应用通常要求数据存储具有更高的可伸缩性和灵活性。 表存储提供了具有高可用性且可大规模伸缩的存储，因此应用可以自动伸缩以满足用户需求。 表存储是 Microsoft 的 NoSQL 键/属性存储-它具有无架构的设计，使其不同于传统的关系数据库。 采用无模式的数据存储，可以很容易地随着应用需求的发展使数据适应存储。 表存储易于使用，因此开发人员可以快速创建应用。

表存储是一种“键-属性”存储，这意味着表中的每个值都是随所键入的一个属性名称存储的。 属性名称用来筛选和指定选择条件。 属性集合及其值构成了实体。 由于表存储是无模式的，因此同一表中的两个实体可以包含不同的属性集合，并且这些属性可以属于不同的类型。

可以使用表存储来存储灵活的数据集，例如 Web 应用的用户数据、通讯簿、设备信息，以及服务需要的任何其他类型的元数据。 对于当前的基于 Internet 的应用，NoSQL 数据库（例如表存储）提供了一种用于替代传统的关系数据库的主流方式。

一个存储帐户可以包含任意数目的表，一个表可以包含任意数目的实体，最高可达存储帐户的容量限制。

### <a name="queue-storage"></a>队列存储

在设计应用以实现可伸缩性时，通常要将各个应用组件分离，使其可以独立地进行伸缩。 队列存储为在应用组件之间进行异步通信提供了一种可靠的消息传送解决方案，无论这些应用组件是在云中、在桌面上、在本地服务器上运行还是在移动设备上运行。 队列存储还支持管理异步任务以及构建过程工作流。

一个存储帐户可以包含任意数目的队列，一个队列可以包含任意数目的消息（最高可达存储帐户的容量限制）。 每条消息最大可以为 64 KB。

## <a name="next-steps"></a>后续步骤

- [与 Azure 一致的存储：差异和注意事项](azure-stack-acs-differences.md)

- 若要了解有关 Azure 存储的详细信息，请参阅 [Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
