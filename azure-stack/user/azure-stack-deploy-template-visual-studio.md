---
title: 在 Azure Stack Hub 中通过 Visual Studio 部署模板 |Microsoft Docs
description: 了解如何在 Azure Stack Hub 中通过 Visual Studio 部署模板。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 2aa7d6be221ad9c877290dc87d98ceec5483c6a9
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883705"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>使用 Visual Studio 在 Azure Stack 中心部署模板

可以使用 Visual Studio 将 Azure 资源管理器模板部署到 Azure Stack 中心。

## <a name="to-deploy-a-template"></a>部署模板

1. [安装 Visual Studio 并连接](azure-stack-install-visual-studio.md)到 Azure Stack 集线器。
2. 打开 Visual Studio。
3. 选择 "**文件**"，然后选择 "**新建**"。 在 "**新建项目**" 中，选择 " **Azure 资源组**"。
4. 输入新项目的**名称**，然后选择 **"确定"** 。
5. 在 "**选择 Azure 模板**" 中，从下拉列表中选择**Azure Stack 集线器快速入门**"。
6. 选择 " **101-创建-存储-帐户**"，然后选择 **"确定"** 。
7. 在新项目中，展开 "**解决方案资源管理器**中的"**模板**"节点，查看可用的模板。
8. 在**解决方案资源管理器**中，选择项目的名称，然后选择 "**部署**"。 选择 "**新建部署**"。
9. 在 "**部署到资源组**" 中，使用 "**订阅**" 下拉列表选择 Microsoft Azure Stack 中心订阅。
10. 从 "**资源组**" 列表中，选择现有资源组或创建新资源组。
11. 从 "**资源组位置**" 列表中选择一个位置，然后选择 "**部署**"。
12. 在 "**编辑参数**" 中，提供参数的值（根据模板的不同），然后选择 "**保存**"。

## <a name="next-steps"></a>后续步骤

* [通过命令行部署模板](azure-stack-deploy-template-command-line.md)
* [为 Azure Stack 集线器开发模板](azure-stack-develop-templates.md)
