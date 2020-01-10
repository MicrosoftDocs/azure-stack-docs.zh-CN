---
title: 用于在边缘使用 Azure 和 Azure Stack 中心训练机器学习（ML）模型的模式。
description: 了解如何使用 Azure 和 Azure Stack 中心服务在边缘执行 ML 模型培训。
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 3f16b543751ac4bee94cc05045c03de7230f9a3c
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717971"
---
# <a name="train-machine-learning-ml-model-at-the-edge-pattern"></a>在边缘模式训练机器学习（ML）模型

从仅存在于本地的数据生成可移植 ML 模型。

## <a name="context-and-problem"></a>上下文和问题

很多组织想要使用数据科学家理解的工具从其本地数据或旧数据中解除见解。 [Azure 机器学习](/azure/machine-learning/)提供了用于训练、调整和部署 ML 和深度学习模型的云本机工具。  

但是，某些数据太大，无法发送到云，或者出于法规原因无法发送到云。 使用此模式，数据科学家可以使用 Azure 机器学习来使用本地数据和计算来定型模型。 

## <a name="solution"></a>解决方案

边缘模式的定型使用 Azure Stack 集线器上运行的虚拟机（VM）。 VM 在 Azure ML 中注册为计算目标，使其只能访问本地可用的数据。 在这种情况下，数据存储在 Azure Stack 集线器的 blob 存储中。 

在对模型进行训练后，会向 Azure ML 注册，并将其添加到 Azure 容器注册表以进行部署。 对于这种模式迭代，Azure Stack 中心训练 VM 必须可通过公共 Internet 访问。 

[![在边缘体系结构上定型 ml 模型](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

解决方案的工作原理如下： 

1. 使用 Azure ML 作为计算目标部署和注册 Azure Stack 中心 VM。
2. 在 Azure ML 中创建了一个使用 Azure Stack 中心 VM 作为计算目标的实验。
3. 在对模型进行训练后，将对其进行注册和容器化。
4. 现在可以将模型部署到本地或云中的位置。

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | Description |
|----------|-----------|-------------|
| Azure | Azure 机器学习 | [Azure 机器学习](/azure/machine-learning/)协调 ML 模型的培训。 |
| | Azure 容器注册表 | Azure ML 将模型打包到容器中，并将其存储在[Azure 容器注册表](/azure/container-registry/)中进行部署。|
| Azure Stack Hub | 应用服务 | [应用服务 Azure Stack 集线器](/azure-stack/operator/azure-stack-app-service-overview)为边缘提供组件的基础。 |
| | 计算 | 使用 Docker 运行 Ubuntu 的 Azure Stack 集线器 VM 用于训练 ML 模型。 |
| | 存储空间 | 可以在 Azure Stack Hub blob 存储中承载私有数据。 |

## <a name="issues-and-considerations"></a>问题和注意事项

决定如何实现此解决方案时，请注意以下几点：

### <a name="scalability"></a>可伸缩性 

若要启用此解决方案进行缩放，需要在 Azure Stack 集线器上创建适当大小的虚拟机以进行培训。

### <a name="availability"></a>可用性

确保训练脚本和 Azure Stack 中心 VM 有权访问用于定型的本地数据。

### <a name="manageability"></a>可管理性

确保模型和试验经过适当的注册、版本控制和标记，以避免在模型部署过程中出现混乱。 

### <a name="security"></a>安全性

此模式允许 Azure ML 访问本地可能的敏感数据。 确保用于通过 SSH 连接到 Azure Stack 中心 VM 的帐户具有强密码，而训练脚本不会将数据保留或上传到云。 

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 有关 ML 和相关主题的概述，请参阅[Azure 机器学习文档](/azure/machine-learning)。
- 请参阅[Azure 容器注册表](/azure/container-registry/)，了解如何为容器部署生成、存储和管理映像。
- 请参阅[Azure Stack 集线器上的应用服务](/azure-stack/operator/azure-stack-app-service-overview)，详细了解资源提供程序和部署方式。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请[在边缘部署指南中继续学习训练 ML 模型](https://aka.ms/edgetrainingdeploy)。 部署指南提供了有关部署和测试其组件的分步说明。