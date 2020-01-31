---
title: 适用于 Azure 和 Azure Stack 的混合模式和解决方案示例
description: 混合模式和解决方案示例的概述，有助于在 Azure 和 Azure Stack 上学习和构建混合解决方案。
author: BryanLa
ms.topic: overview
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 7b0eedab9981556c06b3f0d102e376b0eb519256
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875753"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>适用于 Azure 和 Azure Stack 的混合模式和解决方案示例

Microsoft 将 Azure 和 Azure Stack 产品和解决方案作为一个一致的 Azure 生态系统提供。 Microsoft Azure Stack 系列是 Azure 的扩展。 

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>混合云和混合应用

Azure Stack 通过启用*混合云*，将云计算的灵活性带入本地环境和边缘。 Azure Stack 集线器、Azure Stack HCI 和 Azure Stack 边缘将 Azure 从云中扩展到主权数据中心、分支机构、现场，等等。 利用这一组不同的功能，你可以：

- 在 Azure 和本地环境中一致地重复使用代码和运行云本机应用。
- 通过可选的 Azure 服务连接运行传统的虚拟化工作负荷。
- 将数据传输到云，或将数据保留在主权数据中心以保持符合性。
- 在智能边缘运行硬件加速的计算机学习、容器化或虚拟化工作负荷。

跨云的应用程序也称为*混合应用程序*。 可以在 Azure 中构建混合云应用，并将其部署到位于任何位置的连接或断开连接的数据中心。

混合应用程序方案与可用于开发的资源差别很大。 它们还跨越一些注意事项，如地域、安全性、Internet 访问等。 尽管此处所述的模式和解决方案可能无法满足所有要求，但它们提供了在实现混合解决方案时要探索和重复使用的准则和示例。

## <a name="design-patterns"></a>设计模式

设计模式从真实的客户方案和经验中剔除了通用化的可重复设计指南。 模式是抽象的，使其适用于不同类型的方案或垂直行业。 每个模式都记录了上下文和问题，并提供了解决方案示例的概述。 解决方案示例旨在作为模式的可能实现。

有两种类型的模式项目：

- 单模式：提供适用于单个通用方案的设计指南。
- 多模式：提供使用多种模式的应用程序的设计指南。 这通常是解决更复杂的方案或行业特定问题所必需的。

## <a name="solution-deployment-guides"></a>解决方案部署指南

逐步部署指南有助于部署解决方案示例。 本指南还可能引用配套的代码示例，该示例存储在 GitHub[解决方案示例](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)存储库中。 

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。
- 浏览目录中的 "模式" 和 "解决方案部署指南" 部分，以了解有关每个的详细信息。
- 阅读[混合应用程序设计注意事项](overview-app-design-considerations.md)，了解用于设计、部署和操作混合应用程序的软件质量的支柱。
- [在 Azure Stack 上设置开发环境](../user/azure-stack-dev-start.md)，并在 Azure Stack 上[部署你的第一个应用](../user/azure-stack-dev-start-deploy-app.md)。
