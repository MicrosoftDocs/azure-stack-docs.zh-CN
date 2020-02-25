---
title: 使用 Azure 和 Azure Stack 中心部署基于 AI 的 footfall 检测解决方案
description: 了解如何使用 Azure 和 Azure Stack 中心部署 footfall 检测解决方案。 此解决方案用于分析零售商店中的访问者流量。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 66f6e316c83e25651dc0e0b1be95d6a8467f7f47
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568378"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>使用 Azure 和 Azure Stack 中心部署基于 AI 的 footfall 检测解决方案

本文介绍如何使用 Azure、Azure Stack 中心和自定义视觉 AI 开发工具包部署一个解决方案，该解决方案可通过现实世界操作生成见解。

在此解决方案中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 在边缘部署云本机应用程序捆绑包（CNAB）。 
> - 部署跨云边界的应用程序。
> - 使用自定义视觉 AI 开发工具包在边缘进行推断。

> [!Tip]  
> ![hybrid-pillars](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。

## <a name="prerequisites"></a>先决条件 

在开始本部署指南之前，请确保：

- 查看[Footfall 检测解决方案概述](pattern-retail-footfall-detection.md) 
- 获取用户对 Azure Stack 开发工具包（ASDK）或 Azure Stack 中心集成系统实例的访问权限，其中包括：
  - [Azure Stack 中心资源提供程序上](../operator/azure-stack-app-service-overview.md)安装了 Azure App Service。 需要操作员访问 Azure Stack 中心实例，或与管理员合作来安装。
  - 提供应用服务和存储配额的产品/服务的订阅。 需要操作员访问权限才能创建产品/服务。
- 获取对 Azure 订阅的访问权限
  - 如果你没有 Azure 订阅，请在开始前注册一个[免费试用帐户](https://azure.microsoft.com/free/)。
- 在目录中创建两个服务主体：
  - 一个配置用于 Azure 资源，并具有 Azure 订阅范围的访问权限。 
  - 一个配置用于 Azure Stack 中心资源，并具有 Azure Stack 中心订阅范围的访问权限。 
  - 若要详细了解如何创建服务主体和授权访问，请参阅[使用应用标识访问资源](../operator/azure-stack-create-service-principals.md)。 如果希望使用 Azure CLI，请参阅[使用 Azure CLI 创建 Azure 服务主体](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)。
- 在 Azure 或 Azure Stack 中心部署 Azure 认知服务。
  - 首先，[详细了解认知服务](https://azure.microsoft.com/services/cognitive-services/)。
  - 然后，请访问将[Azure 认知服务部署到 Azure Stack 中心](../user/azure-stack-solution-template-cognitive-services.md)，在 Azure Stack 中心部署认知服务。 首先需要注册预览版的访问权限。
- 克隆或下载未配置的 Azure 自定义视觉 AI 开发工具包。 有关详细信息，请参阅[远景 AI DevKit](https://azure.github.io/Vision-AI-DevKit-Pages/)。
- 注册 Power BI 帐户。
- Azure 认知服务人脸 API 订阅密钥和终结点 URL。 可以获取[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)免费试用版。 或者，按照[创建认知服务帐户](/azure/cognitive-services/cognitive-services-apis-create-account)中的说明进行操作。
- 安装以下开发资源：
  - [Azure CLI 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community)
  - [Porter](https://porter.sh/)。 使用 Porter 来部署云应用程序，使用为你提供的 CNAB 捆绑清单。
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [适用于 Visual Studio Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  - [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-application"></a>部署混合云应用程序

首先，使用 Porter CLI 生成凭据集，然后部署云应用程序。  

1. 从 https://github.com/azure-samples/azure-intelligent-edge-patterns克隆或下载解决方案示例代码。 

1. Porter 将生成一组将自动部署应用程序的凭据。 在运行凭据生成命令之前，请确保具有以下各项：

    - 用于访问 Azure 资源的服务主体，包括服务主体 ID、密钥和租户 DNS。
    - 你的 Azure 订阅的订阅 ID。
    - 用于访问 Azure Stack 集线器资源的服务主体，包括服务主体 ID、密钥和租户 DNS。
    - Azure Stack 中心订阅的订阅 ID。
    - Azure 认知服务人脸 API 密钥和资源终结点 URL。

1. 运行 Porter credential 生成过程，并按照提示进行操作：

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. Porter 还需要一组要运行的参数。 创建参数文本文件，并输入以下名称/值对。 如果需要有关任何所需值的帮助，请咨询 Azure Stack 中心管理员。

   > [!NOTE] 
   > `resource suffix` 值用于确保部署的资源在 Azure 中具有唯一的名称。 它必须是字母和数字的唯一字符串，不能超过8个字符。

    ```
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   保存文本文件并记下其路径。

1. 现已准备好使用 Porter 部署混合云应用程序。 运行 install 命令并监视资源是否部署到 Azure 和 Azure Stack 中心：

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. 部署完成后，请记下以下值：
    - 照相机的连接字符串。
    - 映像存储帐户连接字符串。
    - 资源组名称。

## <a name="prepare-the-custom-vision-ai-dev-kit"></a>准备自定义视觉 AI 开发工具包

接下来，设置自定义视觉 AI 开发工具包，如[远景 Ai DevKit 快速入门](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/)中所示。 你还可以使用上一步骤中提供的连接字符串来设置和测试相机。

## <a name="deploy-the-camera-application"></a>部署相机应用程序

使用 Porter CLI 生成凭据集，然后部署相机应用程序。

1. Porter 将生成一组将自动部署应用程序的凭据。 在运行凭据生成命令之前，请确保具有以下各项：
    
    - 用于访问 Azure 资源的服务主体，包括服务主体 ID、密钥和租户 DNS。
    - 你的 Azure 订阅的订阅 ID。
    - 在部署云应用程序时提供的映像存储帐户连接字符串。

1. 运行 Porter credential 生成过程，并按照提示进行操作：

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. Porter 还需要一组要运行的参数。 创建参数文本文件，并输入以下文本。 如果您不知道某些必需的值，请向您的 Azure Stack 中心管理员咨询。

    > [!NOTE]
    > `deployment suffix` 值用于确保部署的资源在 Azure 中具有唯一的名称。 它必须是字母和数字的唯一字符串，不能超过8个字符。

    ```
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    保存文本文件并记下其路径。

4. 现在，可以使用 Porter 部署照相机应用程序了。 运行 "安装" 命令，并在创建 IoT Edge 部署时监视。

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. 查看 `https://<camera-ip>:3000/`上的照相机馈送，其中 `<camara-ip>` 为相机 IP 地址，以验证相机的部署是否已完成。 此步骤可能需要长达10分钟的时间。

## <a name="configure-azure-stream-analytics"></a>配置 Azure 流分析

现在，数据已从照相机流向 Azure 流分析，我们需要手动授权它与 Power BI 通信。

1.  从 "Azure 门户打开"**所有资源**"，并将*footfall\[yoursuffix\]* 作业。

2.  在“流分析作业”窗格的“作业拓扑”部分，选择“输出”选项。

3.  选择**流量输出**输出接收器。

4.  选择 "**续订授权**"，并登录到你的 Power BI 帐户。
    
    ![续订授权提示](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5.  保存输出设置。

6.  请在 "**概述**" 窗格中，选择 "**开始**"，开始将数据发送到 Power BI。

7.  选择“现在”作为作业输出启动时间，然后选择“启动”。 可以在通知栏中查看作业状态。

## <a name="create-a-power-bi-dashboard"></a>创建 Power BI 仪表板

1.  作业成功以后，请导航到 [Power BI](https://powerbi.com/)，然后使用工作或学校帐户登录。 如果流分析作业查询输出了结果，则您创建的*footfall*数据集在 "**数据**集" 选项卡下。

2.  在 Power BI 工作区中，选择 " **+ 创建**" 创建名为 " *Footfall 分析*" 的新仪表板。

3.  在窗口顶部，选择“添加磁贴”。 然后选择“自定义流数据”和“下一步”。 选择**数据**集下的**footfall 数据集**。 从 "**可视化效果类型**" 下拉列表中选择 "**卡**"，并向**字段**添加**age** 。 选择“下一步”，为磁贴输入一个名称，然后选择“应用”，创建该磁贴。

4.  您可以根据需要添加其他字段和卡。

## <a name="test-your-solution"></a>测试你的解决方案

观察在 Power BI 中创建的卡片中的数据如何变化，因为不同的人会在相机前面进行演练。 记录后，推断可能需要长达20秒的时间。

## <a name="remove-your-solution"></a>删除你的解决方案

如果要删除解决方案，请使用 Porter 运行以下命令，并使用为部署创建的相同参数文件： 

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```
## <a name="next-steps"></a>后续步骤

- 了解有关[混合 cloud 应用设计注意事项](overview-app-design-considerations.md)的详细信息
- 查看并建议[在 GitHub 上改进此示例的代码](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis)。
