---
title: 适用于 Azure 和 Azure Stack 集线器的混合模式和解决方案示例
description: 概述在 Azure 和 Azure Stack 集线器上学习和构建混合解决方案的混合模式和解决方案示例。
author: BryanLa
ms.topic: overview
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d56957c139728898117eab5f8a3a35c98722c0d2
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "80812299"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>适用于 Azure 和 Azure Stack 的混合模式与解决方案示例

Microsoft 通过单个一致的 Azure 生态系统提供 Azure 和 Azure Stack 产品与解决方案。 Microsoft Azure Stack 系列是 Azure 的扩展。

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>混合云和混合应用

Azure Stack 通过启用*混合云*，将云计算的灵活性带入本地环境和边缘。 Azure Stack Hub、Azure Stack HCI 和 Azure Stack Edge 将 Azure 从云扩展到主权数据中心、分支机构、现场和更远的范围。 利用这组多样化的功能，可以：

- 重复使用代码并在 Azure 与本地环境中一致地运行云原生应用。
- 使用 Azure 服务的可选连接来运行传统的虚拟化工作负荷。
- 将数据传输到云，或将数据保留在主权数据中心以保持符合性。
- 运行硬件加速的机器学习、容器化或虚拟化工作负荷，所有这些操作都可以在智能边缘进行。

跨云的应用也称为*混合应用*。 你可以在 Azure 中构建混合云应用，并将其部署到位于任何位置的已连接或已断开连接的数据中心。

混合应用方案与可用于开发的资源差别很大。 它们还跨越一些注意事项，如地域、安全性、internet 访问等。 尽管此处所述的模式和解决方案可能无法解决所有要求，但提供了指导原则和示例供用户探索，并可以在实施混合解决方案时重复使用。

## <a name="design-patterns"></a>设计模式

设计模式从真实的客户方案和经验中得出一般化且可重复的设计指导。 模式是抽象的，可适用于不同类型的方案或垂直行业。 每种模式阐述了上下文和问题，并提供解决方案示例的概述。 解决方案示例旨在用作模式的可能实施方案。

模式文章有两种类型：

- 单模式：提供适用于单个通用方案的设计指导。
- 多模式：提供使用多模式应用程序的设计指导。 此模式通常是解决更复杂的方案或行业特定的问题所必需的。

## <a name="solution-deployment-guides"></a>解决方案部署指南

分步式部署指南可帮助用户部署解决方案示例。 该指南也可能会参考 GitHub [解决方案示例存储库](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)中存储的随附代码示例。

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。
- 浏览目录中的 "模式" 和 "解决方案部署指南" 部分，详细了解每个。
- 阅读[混合应用设计注意事项](overview-app-design-considerations.md)，了解用于设计、部署和操作混合应用的软件质量的支柱。
- [在 Azure Stack 上设置开发环境](../user/azure-stack-dev-start.md)和在 Azure Stack 上[部署第一个应用](../user/azure-stack-dev-start-deploy-app.md)。
