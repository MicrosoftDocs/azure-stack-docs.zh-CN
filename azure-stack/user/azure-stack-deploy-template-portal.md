---
title: 使用 Azure Stack Hub 门户部署模板
description: 了解如何使用 Azure Stack Hub 门户部署模板。
author: mattbriggs
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/05/2020
ms.openlocfilehash: b428c99f7c8b847e575e40af2915a4da60f2312f
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778078"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>使用 Azure Stack Hub 门户部署模板

可以使用 Azure Stack 集线器用户门户将 Azure 资源管理器模板部署到 Azure Stack 中心。

## <a name="to-deploy-a-template"></a>部署模板

1. 登录到 Azure Stack 集线器用户门户，选择 " **+ 创建资源**" "  >  **自定义**  >  **模板部署**"。

   ![在 Azure Stack Hub 门户中创建资源](media/azure-stack-deploy-template-portal/template-deploy1.png)

2. 你可以选择 " **类型" 开始筛选器** 以选择 GitHub 快速入门模板，或 **在编辑器中选择 "构建你自己的模板**"。

   ![在 Azure Stack Hub 门户中部署模板](media/azure-stack-deploy-template-portal/template-deploy2.png)

    [**Test-azurestack-模板**](https://github.com/Azure/AzureStack-QuickStart-Templates) 由 Azure Stack 中心社区的成员创建，而不是由 Microsoft 创建。 每个模板由其所有者（而非 Microsoft）根据许可协议授权给你。 Microsoft 不对这些模板负责，也不检查其安全性、符合性或性能方面的问题。 任何 Microsoft 支持计划或服务均不支持社区模板，并 *按原样* 提供，无任何形式的保证。

3. 如果在编辑器中选择了 " **生成您自己的模板**"，请将 JSON 模板代码粘贴到代码窗口中。

   ![在 Azure Stack 中心门户中编辑模板](media/azure-stack-deploy-template-portal/template-deploy3.png)

    - 选择 " **快速启动模板** " 以在编辑器中加载社区模板。

    - 选择 " **加载文件** "，将 Azure 资源管理器模板从本地计算机加载到编辑器中。

    - 选择 " **下载** "，将 Azure 资源管理器模板保存到本地计算机。

    完成对模板的更改后，请选择 " **保存**"。

4. 选择“订阅”。 选择要使用的订阅。 选择“资源组”。 可以选择现有资源组或创建新资源组，然后选择 **"确定"**。 然后选择 " **查看 + 创建**"。

   ![在 Azure Stack 集线器门户中编辑参数](media/azure-stack-deploy-template-portal/template-deploy4.png)

5. 选择“创建”****。

   ![在 Azure Stack 中心门户中选择 "订阅"](media/azure-stack-deploy-template-portal/template-deploy5.png)

6. 仪表板上的新磁贴会跟踪模板部署的进度。

   ![在 Azure Stack 集线器门户中选择资源组](media/azure-stack-deploy-template-portal/template-deploy6.png)

   可使用 Azure 资源管理器 模板通过单个协调操作部署和预配应用程序的所有资源。 还可以重新部署模板，对资源组中的资源进行更改。 有关将模板与 Azure Stack 中心一起使用的详细信息，请参阅 [在 Azure Stack 中心使用 Azure 资源管理器模板](azure-stack-arm-templates.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关部署模板的详细信息，请参阅以下文章：

- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
