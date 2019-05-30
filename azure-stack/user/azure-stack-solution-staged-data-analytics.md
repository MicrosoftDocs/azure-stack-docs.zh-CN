---
title: 使用 Azure 和 Azure Stack 创建临时数据分析解决方案 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 创建临时数据分析解决方案。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 9359c1393229709fc77ee08216a80a26de9135dc
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252010"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>教程：使用 Azure 和 Azure Stack 创建临时数据分析解决方案 

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何使用本地和公有云环境来满足多设施企业的需求。 Azure Stack 提供快速、安全且灵活的解决方案，用于收集、处理、存储和分配本地和远程数据，尤其适用于安全性、保密性、企业策略和法规要求因位置和用户而异的情况。

在此模式中，客户收集的数据需要在收集点进行分析，以便进行快速决策。 这种数据收集通常是在没有 Internet 访问的情况下进行的。 建立连接后，可能需要对数据进行资源密集型分析以获取更多的见解。 仍可在来不及使用公有云或无法使用共有云的情况下分析数据。

在本教程中，请构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建原始数据存储 Blob。
> - 创建新的 Azure Stack 函数，将纯净数据从 Azure Stack 移到 Azure。
> - 创建 Blob 存储触发的函数。
> - 创建包含 Blob 和队列的 Azure Stack 存储帐户。
> - 创建队列触发的函数。
> - 测试队列触发的函数。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> 白皮书 [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars)（混合应用程序的设计注意事项）回顾了设计、部署和运行混合应用程序所需的软件质量要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。

## <a name="prerequisites"></a>必备组件

构建此解决方案需要做一些准备：

-   安装好的能够正常运行的 Azure Stack[Azure Stack 概述](azure-stack-storage-overview.md)

-   Azure 订阅。 (创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   下载并安装 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)。

-   需提供自己的由函数处理的数据。 必须生成数据并确保其能够上传到 Azure Stack 存储 Blob 容器。

## <a name="issues-and-considerations"></a>问题和注意事项

### <a name="scalability-considerations"></a>可伸缩性注意事项

Azure Functions 和存储解决方案可以通过缩放来满足数据量和处理方面的需求。 有关 Azure 可伸缩性的信息和目标，请参阅 [Azure 可伸缩性文档](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

### <a name="availability-considerations"></a>可用性注意事项

就此模式来说，存储是主要的可用性注意事项。 处理和分配大量的数据时，必须通过快速链路进行连接。

### <a name="manageability-considerations"></a>可管理性注意事项

考虑如何通过开发工具和源代码管理来管理解决方案。

## <a name="create-the-raw-data-storage-blob"></a>创建原始数据存储 Blob

存储帐户和 Blob 容器将存储本地活动生成的所有原始数据，包括计算机活动和员工活动、设施数据、生产指标以及其他报告内容。

1.  登录到 [  Azure Stack 门户](https://portal.local.azurestack.external/)。

2.  在 Azure Stack 门户中展开左侧的菜单，打开服务菜单，然后选择“所有服务”。  向下滚动到“存储”  ，选择“存储帐户”  。 在“存储帐户”窗口中，选择“添加”。 

3.  使用下述适用于帐户的信息：

    a.  姓名：**由用户选择**

    b.  部署模型：**资源管理器**

    c.  帐户类型：**存储(常规用途 V1)**

    d.  位置：**美国西部**

    e.  复制：**本地冗余存储 (LRS)**

    f.  性能：**标准**

    g.  需要安全传输：**已禁用**

    h.  订阅：选择一个

    i.  资源组：指定新资源组或选择现有资源组

    j.  配置虚拟网络：**已禁用**

4.  选择“创建”  以创建存储帐户。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  创建后，选择存储帐户的名称。

6.  在帐户边栏选项卡的“Blob 服务”标题下，选择“容器”。  

7.  在边栏选项卡顶部，选择“+ 容器”  。 然后选择“容器”。 

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  姓名：**由你选择**

9.  公共访问级别：**容器**(对容器和 Blob 进行匿名读取访问)

10.  选择“确定”  。

## <a name="create-an-azure-stack-function"></a>创建 Azure Stack 函数

创建新的 Azure Stack 函数，将纯净数据从 Azure Stack 移到 Azure。

### <a name="create-the-azure-stack-function-app"></a>创建 Azure Stack 函数应用

1. 登录到 [Azure Stack 门户](https://portal.local.azurestack.external)。
2. 选择“所有服务”  。
3. 在“Web + 移动”组中选择“Function App”。  

4.  使用图像下面的表中指定的设置创建函数应用。

    | 设置 | 建议的值 | 描述 |
    | ---- | ---- | ---- |
    | 应用程序名称 | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 `a`-`z`、`0``-9` 和 `-`。 |
    | 订阅 | 订阅 | 要在其下创建此新函数应用的订阅。 |
    | **资源组** |  |  |
    | myResourceGroup | 要在其中创建 Function App 的新资源组的名称。 |  |
    | 操作系统 | Windows | 仅当在 Windows 上运行时，才能使用无服务器托管。 |
    | **托管计划** |  |  |
    | 消耗量计划 | 定义如何将资源分配给 Function App 的托管计划。 在默认的消耗计划中，根据函数需求动态添加资源。 在此无服务器托管中，只需为函数运行时间付费。 |  |
    | 位置 | 最靠近自己的区域 | 选择离自己近或离函数访问的其他服务近的区域。 |
    | **存储帐户** |  |  |
    | \<上面创建的存储帐户> | Function App 使用的新存储帐户的名称。 存储帐户名称的长度必须为 3 到 24 个字符。 名称只能使用数字和小写字母。 也可以使用现有帐户。 |  |

    **示例：**

    ![定义新的函数应用设置](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  选择“创建”  以预配和部署函数应用。

6.  选择门户右上角的“通知”图标，留意是否显示“部署成功”消息。 

    ![定义新的函数应用设置](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  选择“转到资源”  ，查看新的函数应用。

![已成功创建 Function App。](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>将函数添加到 Azure Stack 函数应用

1.  创建新的函数，方法是：单击“函数”  ，然后单击“+新建函数”按钮。 

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  选择“计时器触发器”。 

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  选择 **C\#** 作为语言并对函数进行命名：`upload-to-azure`  将计划设置为 `0 0 * * * *`，在 CRON 表示法中这表示每小时一次。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>创建 Blob 存储触发的函数

1. 展开函数应用，选择“函数”旁边的 + 按钮   。

2. 在搜索栏中键入 `blob`，然后选择需要用于“Blob 触发器”模板的语言。 

   ![选择 Blob 存储触发器模板。](media/azure-stack-solution-staged-data-analytics/image10.png)

3. 使用下表中指定的设置：

   | 设置 | 建议的值 | 描述 |
   | ------- | ------- | ------- |
   | 名称 | 在 Function App 中唯一 | 此 Blob 触发函数的名称。 |
   | 路径 | \<上述存储位置的路径> | 所监视的 Blob 存储中的位置。 Blob 的文件名作为 name 参数在绑定中传入。 |
   | 存储帐户连接 | 函数应用连接 | 可以使用函数应用已在使用的存储帐户连接，也可以创建一个新的存储帐户连接。 |

   **示例：**

   ![创建 Blob 存储触发的函数。](media/azure-stack-solution-staged-data-analytics/image11.png)

4. 选择“创建”  创建该函数。

### <a name="test-the-function"></a>测试函数

1.  在 Azure 门户中，浏览到该函数。 展开页面底部的“日志”  ，确保日志流式处理未暂停。

2.  打开存储资源管理器，连接到在此部分开头创建的存储帐户。

3.  展开存储帐户、“Blob 容器”以及此前创建的 Blob。  选择“上传”  ，然后选择“上传文件”  。

    ![将文件上传到 blob 容器。](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  在“上传文件”对话框中，选择“文件”字段。 浏览到本地计算机上的文件（如图像文件），将其选中，然后依次选择“打开”  和“上传”  。

5.  返回到函数日志并验证是否已读取 Blob。

    **示例：**

    ![查看日志中的消息。](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>创建 Azure Stack 存储帐户

创建包含 Blob 和队列的 Azure Stack 存储帐户。

### <a name="storage-blob--data-archiving"></a>存储 Blob 数据存档

此存储帐户将存储两个容器。 这两个容器一个是 Blob，用于保存存档数据；另一个是队列，用于处理那些指定由总部分配的数据。

请使用上述步骤和设置创建另一存储帐户和 Blob 容器作为存档存储。

### <a name="storage-queue--filtered-data-holding"></a>存储队列筛选的数据保留

1.  使用上述步骤和设置访问新的存储帐户。

2.  在“存储帐户概览”部分，选择“队列”。 

3.  选择“+ 队列”  ，填充“名称”字段，为新队列填充一个名称。 

4.  选择“确定”  。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>创建队列触发的函数

1.  执行上述函数创建部分的步骤，使用队列触发器而不是 Blob 触发器创建另一个 Azure Stack 函数。

2.  使用下表中指定的设置：

    | 设置 | 建议的值 | 描述 |
    | ------- | ------- | ------- |
    | 名称 | 在 Function App 中唯一 | 此队列触发函数的名称。 |
    | 路径 | \<上述存储位置的路径> | 所监视的存储中的位置。 队列的文件名作为 name 参数在绑定中传入。 |
    | 存储帐户连接 | 函数应用连接 | 可以使用函数应用已在使用的存储帐户连接，也可以创建一个新的存储帐户连接。 |

3.  选择“创建”  创建该函数。

## <a name="test-the-queue-triggered-function"></a>测试队列触发的函数

1. 在 Azure Stack 门户中，浏览到该函数。 展开页面底部的“日志”  ，确保日志流式处理未暂停。

2. 打开存储资源管理器，连接到在此部分开头创建的存储帐户。

3. 展开存储帐户、“Blob 容器”以及此前创建的 Blob。  选择“上传”  ，然后选择“上传文件”  。

   ![将文件上传到 blob 容器。](media/azure-stack-solution-staged-data-analytics/image12.png)

4. 在“上传文件”对话框中，选择“文件”字段。 浏览到本地计算机上的文件（如图像文件），将其选中，然后依次选择“打开”  和“上传”  。

5. 返回到函数日志并验证是否已读取 Blob。

   **示例：**

   ![查看日志中的消息。](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>安全地存储和访问合规数据

可以通过 Azure 和 Azure Stack 临时数据分析以及自定义终结点指令安全地存储、处理、分配和访问全球企业数据。 远程办公员工和机器的活动、设施数据以及业务指标会按照公司政策和地区法规持续进行编译、存储、符合性测试以及分配。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
