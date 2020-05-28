---
title: 使用 Azure Stack Hub 门户部署模板
description: 了解如何使用 Azure Stack Hub 门户部署模板。
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: ffcc181f0d5693b1c525f871ff5ba92f0b508b04
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112132"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>使用 Azure Stack Hub 门户部署模板

可以使用门户将 Azure 资源管理器模板部署到 Azure Stack Hub。

## <a name="to-deploy-a-template"></a>部署模板

1. 登录到门户中，依次选择“+ 创建资源”  、“自定义”  。

   ![在 Azure Stack Hub 门户中创建资源](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. 选择“模板部署”。 

   ![在 Azure Stack Hub 门户中部署模板](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. 选择“编辑模板”  ，然后将 JSON 模板代码粘贴到代码窗口中。 选择“保存”。 

   ![在 Azure Stack Hub 门户中编辑模板](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. 选择“编辑参数”  ，为显示的参数提供值，然后选择“确定”  。

   ![在 Azure Stack Hub 门户中编辑参数](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. 选择“订阅”。  选择要使用的订阅，然后选择“确定”  。

   ![在 Azure Stack Hub 门户中选择订阅](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. 选择“资源组”  。 选择现有资源组，或创建一个新资源组，然后选择“确定”  。

   ![在 Azure Stack Hub 门户中选择资源组](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. 选择“创建”  。 仪表板上的新磁贴会跟踪模板部署的进度。

   ![在 Azure Stack Hub 门户中创建模板](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>后续步骤

若要了解有关部署模板的详细信息，请参阅以下文章：

- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
