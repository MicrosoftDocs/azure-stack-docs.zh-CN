---
title: 使用 Azure Stack 中的命令行部署模板 |Microsoft Docs
description: 了解如何使用跨平台命令行接口 (CLI) 将模板部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 92c9189f8144804f36e551ab89d8b4fc4c1f8598
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691369"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>使用 Azure Stack 中的命令行部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Azure 命令行接口 (CLI)，将在 Azure Stack 中的 Azure 资源管理器模板部署。 Azure 资源管理器模板部署和预配单个协调的操作中的应用的资源。

## <a name="before-you-begin"></a>开始之前

- 使用 Azure CLI [安装并连接](azure-stack-version-profiles-azurecli2.md)到 Azure Stack。
- 从[创建存储帐户示例模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)下载文件 *azuredeploy.json* 和 *azuredeploy.parameters.json*。

## <a name="deploy-template"></a>部署模板

导航到下载这些文件并将其运行以下命令将模板部署到其中的文件夹：

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

此命令将模板部署到 Azure Stack POC 默认位置中的资源组 **cliRG**。

## <a name="validate-template-deployment"></a>验证模板部署

若要查看此资源组和存储帐户，请使用以下 CLI 命令：

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>后续步骤

详细了解如何[使用 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)。
