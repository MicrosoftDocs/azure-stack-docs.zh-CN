---
title: Azure Stack 中心存储简介 |Microsoft Docs
description: 了解 Azure Stack 集线器存储服务。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 50ca47c66e31417378e5088bf6c0c9f7cb406cc1
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878317"
---
# <a name="introduction-to-azure-stack-hub-storage"></a>Azure Stack 中心存储简介

## <a name="overview"></a>概述

Azure Stack 中心存储是一组与 Azure 存储提供的服务一致的云存储服务。 这些服务包括 blob、表和队列。

## <a name="azure-stack-hub-storage-services"></a>Azure Stack 中心存储服务

Azure Stack 中心存储提供以下三种服务：

- **Blob 存储**

    Blob 存储存储非结构化的对象数据。 Blob 可以是任何类型的文本或二进制数据，例如文档、媒体文件或应用安装程序。

- **表存储**

    表存储可存储结构化数据集。 表存储是一个 NoSQL 关键特性数据存储，可用于快速开发和快速访问大量数据。

- **队列存储**

    队列存储为云服务的各个组件之间的工作流处理和通信提供可靠的消息传送。

Azure Stack 中心存储帐户是一个安全帐户，可让你访问 Azure Stack 中心存储中的服务。 存储帐户为存储资源提供唯一的命名空间。 下图显示了存储帐户中 Azure Stack 中心存储资源之间的关系：

![Azure Stack 中心存储概述](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob 存储

对于有大量非结构化对象数据要存储在云中的用户，blob 存储提供了有效且可缩放的解决方案。 使用 blob 存储来存储如下内容：

- 文档
- 社交数据，例如照片、视频、音乐和博客
- 文件、计算机、数据库和设备的备份
- Web 应用的图像和文本
- 云应用的配置数据
- 大数据，例如日志和其他大型数据集

每个 Blob 都组织到一个容器中。 容器还提供了一种有用的方式来向对象组分配安全策略。 一个存储帐户可以包含任意数目的容器，一个容器可以包含任意数量的 blob （最多可包含存储帐户的限制）。

Blob 存储提供三种类型的 blob：

- **块 Blob**

    块 blob 经过优化，可用于流式传输和存储云对象。 它们是存储文档、媒体文件、备份和其他类似文件的不错选择。

- **追加​​ Blob**

    追加 Blob 类似于块 Blob，但针对追加追加操作进行了优化。 追加 Blob 仅可以通过将新的块添加到末尾来进行更新。 对于需要新数据只能写入到 Blob 结尾的情况，例如日志记录，追加 Blob 是一个不错的选择 。

- **页 Blob**

    页 blob 针对表示 IaaS 磁盘和支持最大为 1 TB 的随机写入进行了优化。 Azure Stack 集线器虚拟机连接的 IaaS 磁盘是作为页 blob 存储的 VHD。

### <a name="table-storage"></a>表存储

现代应用程序通常要求数据存储具有更高的可伸缩性和灵活性，而不是以前的软件必需要求。 表存储提供高度可用且可大规模缩放的存储，以便你的应用可以自动缩放以满足用户需求。 表存储是 Microsoft 的 NoSQL 键/属性存储-它具有无架构的设计，使其不同于传统的关系数据库。 使用无架构数据存储，可以在应用的需求不断变化时轻松调整数据。 表存储易于使用，因此开发人员可以快速创建应用。

表存储是一个键属性存储，这意味着表中的每个值都以类型化属性名称存储。 属性名称用于筛选和指定选择条件。 属性集合及其值构成了实体。 因为表存储是无架构的，所以同一个表中的两个实体可以包含不同的属性集合，并且这些属性可以属于不同的类型。

可以使用表存储来存储灵活的数据集，例如 web 应用的用户数据、通讯簿、设备信息，以及服务需要的任何其他类型的元数据。 对于当前的基于 internet 的应用程序，NoSQL 数据库（如表存储）为传统关系数据库提供了一种常用替代方法。

一个存储帐户可以包含任意数目的表，一个表可以包含任意数量的实体，直至达到存储帐户的容量限制。

### <a name="queue-storage"></a>队列存储

在设计规模的应用程序中，应用程序组件通常是分离的，使其可以独立缩放。 队列存储为应用组件之间的异步通信提供可靠的消息传送解决方案，无论它们是在云中、在桌面上、在本地服务器上运行还是在移动设备上运行。 队列存储还支持管理异步任务以及构建过程工作流。

一个存储帐户可以包含任意数目的队列，一个队列可以包含任意数量的消息（最多可包含存储帐户的容量限制）。 每条消息最大可以为 64 KB。

## <a name="next-steps"></a>后续步骤

- [Azure 一致性存储：差异和注意事项](azure-stack-acs-differences.md)

- 若要了解有关 Azure 存储的详细信息，请参阅[Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
