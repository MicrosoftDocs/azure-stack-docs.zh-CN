---
title: 在 Azure Stack 中心使用门户部署模板 |Microsoft Docs
description: 了解如何使用 Azure Stack 集线器门户来部署模板。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: df7034f60d7e0e4999c4d658b72f47088cb70c3e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816354"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>在 Azure Stack 中心使用门户部署模板

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

可以使用门户将 Azure 资源管理器模板部署到 Azure Stack 中心。

## <a name="to-deploy-a-template"></a>部署模板

1. 登录到门户，选择 " **+ 创建资源**"，然后选择 "**自定义**"。

   ![在 Azure Stack Hub 门户中创建资源](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. 选择“模板部署”。

   ![Azure Stack 中心门户中部署模板](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. 选择 "**编辑模板**"，然后将 JSON 模板代码粘贴到代码窗口中。 选择“保存”。

   ![在 Azure Stack 中心门户中编辑模板](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. 选择 "**编辑参数**"，为显示的参数提供值，然后选择 **"确定"** 。

   ![在 Azure Stack 集线器门户中编辑参数](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. 选择“订阅”。 选择要使用的订阅，然后选择 **"确定"** 。

   ![在 Azure Stack 中心门户中选择 "订阅"](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. 选择 "**资源组**"。 选择现有资源组或创建新资源组，然后选择 **"确定"** 。

   ![在 Azure Stack 集线器门户中选择资源组](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. 选择“创建”。 仪表板上的新磁贴跟踪模板部署的进度。

   ![Azure Stack 中心门户中创建模板](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>后续步骤

若要详细了解如何部署模板，请参阅以下文章：

- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
