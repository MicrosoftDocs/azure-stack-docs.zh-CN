---
title: 在 Azure Stack 中使用命令行部署模板 | Microsoft Docs
description: 了解如何使用 Azure 跨平台命令行接口（CLI）将模板部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 7b3daaefd8fa7e7bce9c6d5708e664911fc906fe
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304089"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>在 Azure Stack 中使用命令行部署模板

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure 命令行接口 (CLI) 在 Azure Stack 中部署 Azure 资源管理器模板。 Azure 资源管理器模板可通过单个协调的操作为应用部署和设置资源。

## <a name="deploy-template"></a>部署模板

1. 浏览[test-azurestack](https://aka.ms/AzureStackGitHub)存储库并查找 " **101-创建-存储-帐户**" 模板。 将模板（`azuredeploy.json`）和参数文件 `(azuredeploy.parameters.json`）保存到本地驱动器上的某个位置，例如 `C:\templates\`
2. 导航到要将文件下载到其中的文件夹。 
3. 使用 Azure CLI [安装并连接](azure-stack-version-profiles-azurecli2.md)到 Azure Stack。
4. 在以下命令中更新区域和位置。 如果使用的是 ASDK，请使用 `local` 作为 location 参数。 若要部署模板，请执行以下操作：
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

此命令将模板部署到 Azure Stack 实例中的资源组**testDeploy** 。

## <a name="validate-template-deployment"></a>验证模板部署

若要查看资源组和存储帐户，请运行以下 CLI 命令：

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>后续步骤

了解如何[使用 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)。
