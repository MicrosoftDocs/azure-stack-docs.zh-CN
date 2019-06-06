---
title: 将 Azure 认知服务部署到 Azure Stack | Microsoft Docs
description: 了解如何将 Azure 认知服务部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: c642a3eeb07ef2ce94ca8dc338a781256c5b9f37
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691231"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>将 Azure 认知服务部署到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 azure 认知服务处于预览状态。

可以在 Azure Stack 上使用支持容器的 Azure 认知服务。 Azure 认知服务中的容器支持可让你使用 Azure 提供的相同丰富 API。 使用容器可以灵活部署和托管 [Docker 容器](https://www.docker.com/what-container)中提供的服务。 容器支持目前以预览版的 Azure 认知服务，包括的部分子集[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)，[人脸](https://docs.microsoft.com/azure/cognitive-services/face/overview)，并[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)，和[语言理解](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)(LUIS)。

容器化是一种方法中的软件分发的应用或服务，包括其依赖项和配置打包为容器映像。 只需进行少量的修改或无需修改，即可将映像部署到容器主机。 每个容器与其他容器和基础操作系统相隔离。 系统本身仅提供运行你的映像所需的组件。 容器主机的占用空间比虚拟机更小。 您还可以通过为短期任务的映像创建容器，并可以删除不再需要时。

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>在 Azure Stack 上使用包含认知服务的容器

- **对数据进行控制**  
  允许应用用户在使用认知服务时控制其数据。 你可向应用程序的用户不能发送数据到全球版 Azure 或公有云提供认知服务。

- **对模型更新进行控制**  
  向应用用户在其解决方案中部署的模型的版本更新。

- **可移植的体系结构**  
  启用可移植应用体系结构的创建，以便可以将你的解决方案部署到公有云，私有云上本地或边缘。 可将容器部署到 Azure Kubernetes 服务，Azure 容器实例，或在 Azure Stack 中的 Kubernetes 群集。 有关详细信息，请参阅[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)。

- **高吞吐量与低延迟**  
   使应用用户能够根据流量高峰进行缩放，以实现高吞吐量与低延迟。 启用认知服务运行在 Azure Kubernetes 服务中以物理方式靠近其应用程序逻辑和数据。

使用 Azure Stack 部署以及用于实现高可用性和弹性缩放应用容器的 Kubernetes 群集中的认知服务容器。 可以通过将与应用服务、 函数、 Blob 存储、 SQL 或 mySQL 数据库上构建的组件结合使用认知服务开发您的应用程序。

有关认知服务容器的更多详细信息，请参阅 [Azure 认知服务中的容器支持](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)。

## <a name="deploy-the-azure-face-api"></a>部署 Azure 人脸 API

本文介绍如何部署 Azure Stack 上的 Kubernetes 群集上的 Azure 人脸 API。 可以使用相同的方法来部署 Azure Stack Kubernetes 群集上的其他认知服务容器。

## <a name="prerequisites"></a>必备组件

在开始之前，你将需要：

1.  请求容器注册表的访问权限，以便能够从 Azure 认知服务容器注册表提取人脸容器映像。 有关详细信息，请参阅[请求访问专用容器注册表](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)。

2.  准备 Azure Stack 上的 Kubernetes 群集。 可以遵循[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md) 一文。

## <a name="create-azure-resources"></a>创建 Azure 资源

若要预览的人脸、 LUIS 或识别的文本容器在 Azure 上创建认知服务资源。 你将需要使用从资源的订阅密钥和终结点 URL 来实例化认知服务容器。

1. 在 Azure 门户中创建 Azure 资源。 若要预览“人脸”容器，必须先在 Azure 门户中创建相应的“人脸”资源。 有关详细信息，请参阅[快速入门：在 Azure 门户中创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。

   > [!Note]
   >  “人脸”或“计算机视觉”资源必须使用 F0 定价层。

2. 获取 Azure 资源的终结点 URL 和订阅密钥。 一旦创建 Azure 资源时，使用从该资源的订阅密钥和终结点 URL 实例化预览相应的人脸、 LUIS 或识别的文本容器。

## <a name="create-a-kubernetes-secret"></a>创建 Kubernetes 机密 

请使用 Kubectl 创建机密命令来访问专用容器注册表。 替换`<username>`的用户名称和`<password>`收到来自 Azure 认知服务团队使用的凭据中提供的密码。

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>准备 YAML 配置文件

使用 YAML 配置文件以简化 Kubernetes 群集上的认知服务的部署。

以下 YAML 示例配置文件可将“人脸”服务部署到 Azure Stack：

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

在此 YAML 配置文件中，使用了用于从 Azure 容器注册表获取认知服务容器映像的机密。 密钥文件用于部署的容器的特定副本。 此外可以创建负载均衡器，请确保用户可以访问此服务从外部。

有关重要字段的详细信息：

| 字段 | 说明 |
| --- | --- |
| replicaNumber | 定义要创建的实例的初始副本。 可以将其缩放，请在部署后更高版本中。 |
| ImageLocation | 指示 ACR 中特定认知服务容器映像的位置。 例如，人脸服务：`aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |在步骤中记下的终结点 URL[创建 Azure 资源](#create-azure-resources) |
| ApiKey | 步骤中记下的订阅密钥[创建 Azure 资源](#create-azure-resources) |
| SecretName | 在步骤 [创建 Kubernetes secret(#create-a-kubernetes-secret) 中创建的密钥名称 |

## <a name="deploy-the-cognitive-service"></a>部署认知服务

以下命令以部署认知服务容器的使用：

```bash  
    Kubectl apply -f <yamlFineName>
```
使用以下命令监视部署方式： 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>测试认知服务

访问[OpenAPI 规范](https://swagger.io/docs/specification/about/)从 **/swagger**该容器的相对 URI。 本规范，以前称为 Swagger 规范，描述由实例化的容器支持的操作。 例如，以下 URI 提供对上一示例中实例化的情绪分析容器 OpenAPI 规范的访问：

```HTTP  
http:<External IP>:5000/swagger
```

可通过以下命令获取外部 IP 地址： 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>尝试使用 Python 来测试服务

可以尝试运行一些简单的 Python 脚本来验证 Azure Stack 上的认知服务。 有的正式 Python 快速入门示例[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)，[人脸](https://docs.microsoft.com/azure/cognitive-services/face/overview)，[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)，以及[语言理解](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)(LUIS) 以供参考。

有需要时使用的 Python 应用以验证在容器中运行的服务，请记住两点： 
1. 在容器中的认知服务不需要子密钥进行身份验证，但仍需要的任何字符串作为一个占位符，以满足 SDK。 
2. Base_URL 替换为实际的服务终结点 IP 地址。

下面是使用人脸服务 Python SDK 来检测和定格图像中的人脸的示例 Python 脚本：

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>后续步骤

[如何安装和运行计算机视觉 API 容器。](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[如何安装和运行人脸 API 容器](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[如何安装和运行文本分析 API 容器](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[如何安装和运行语言理解 (LIUS) 容器](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
