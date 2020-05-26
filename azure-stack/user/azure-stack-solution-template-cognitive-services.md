---
title: 将 Azure 认知服务部署到 Azure Stack 中心
description: 了解如何将 Azure 认知服务部署到 Azure Stack 中心。
author: mattbriggs
ms.topic: article
ms.date: 05/21/2020
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 05/21/2020
ms.openlocfilehash: 07af0665b2bc7993a90100edccc0a4831700c432
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780768"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>将 Azure 认知服务部署到 Azure Stack 中心

可以将 Azure 认知服务与 Azure Stack 集线器上的容器支持配合使用。 Azure 认知服务中的容器支持可让你使用 Azure 提供的相同丰富 API。 使用容器可以灵活部署和托管 [Docker 容器](https://www.docker.com/what-container)中提供的服务。 

容器化是一种软件分发方法，其中应用或服务（包括其依赖项和配置）打包为容器映像。 只需进行少量的修改或无需修改，即可将映像部署到容器主机。 每个容器与其他容器和基础操作系统相隔离。 系统本身仅具有运行映像所需的组件。 容器主机的占用空间比虚拟机更小。 你还可以从映像创建用于短期任务的容器，并且在不再需要时可以将其删除。

容器支持当前适用于 Azure 认知服务的子集：

- 语言理解
- 文本分析（情绪3.0）

> [!IMPORTANT]
> Azure Stack 中心的 Azure 认知服务的子集目前提供公共预览版。
> 提供评审版本时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对于 Azure 认知服务的子集，容器支持目前为公共预览版：

- 读取（光学字符识别 \[ OCR]）
- 关键短语提取
- 语言检测
- 异常探测器
- 窗体识别器
- 语音到文本（自定义，标准）
- 文本到语音转换（自定义、标准）

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>在 Azure Stack 中心使用具有认知服务的容器

- **控制数据**  
  允许应用用户在使用认知服务时控制其数据。 你可以将认知服务传递给无法将数据发送到全球 Azure 或公有云的应用程序用户。

- **控制模型更新**  
  向应用程序用户提供对其解决方案中部署的模型的版本更新。

- **可移植体系结构**  
  支持创建可移植的应用程序体系结构，以便可以将解决方案部署到公有云、本地的私有云或边缘。 可以将容器部署到 Azure Kubernetes 服务、Azure 容器实例或 Azure Stack 集线器中的 Kubernetes 群集。 有关详细信息，请参阅[Deploy Kubernetes to Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)。

- **高吞吐量与低延迟**  
   使应用用户能够根据流量高峰进行缩放，以实现高吞吐量与低延迟。 让认知服务在 Azure Kubernetes 服务中物理地靠近其应用逻辑和数据。

使用 Azure Stack 中心，在 Kubernetes 群集中部署认知服务容器以及应用容器，实现高可用性和弹性缩放。 可以通过将认知服务与在应用服务、函数、Blob 存储、SQL 或 mySQL 数据库上构建的组件相结合来开发应用。

有关认知服务容器的更多详细信息，请参阅 [Azure 认知服务中的容器支持](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)。

## <a name="deploy-the-azure-face-api"></a>部署 Azure 人脸 API

本文介绍如何在 Azure Stack Hub 上的 Kubernetes 群集上部署 Azure 人脸 API。 可以使用相同的方法在 Azure Stack Hub Kubernetes 群集上部署其他认知服务容器。

## <a name="prerequisites"></a>先决条件

在开始之前，您需要：

1.  请求容器注册表的访问权限，以便能够从 Azure 认知服务容器注册表提取人脸容器映像。 有关详细信息，请参阅[请求访问专用容器注册表](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)。

2.  准备 Azure Stack 集线器上的 Kubernetes 群集。 可以按照将[Kubernetes 部署到 Azure Stack 集线器](azure-stack-solution-template-kubernetes-deploy.md)一文进行操作。

## <a name="create-azure-resources"></a>创建 Azure 资源

在 Azure 上创建认知服务资源，以便预览面部、LUIS 或识别文本容器。 需要使用资源的订阅密钥和终结点 URL 来实例化认知服务容器。

1. 在 Azure 门户中创建 Azure 资源。 若要预览“人脸”容器，必须先在 Azure 门户中创建相应的“人脸”资源。 有关详细信息，请参阅[快速入门：在 Azure 门户中创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。

   > [!Note]
   >  “人脸”或“计算机视觉”资源必须使用 F0 定价层。

2. 获取 Azure 资源的终结点 URL 和订阅密钥。 创建 Azure 资源后，使用该资源的 "订阅密钥" 和 "终结点 URL" 来实例化预览的相应人脸、LUIS 或识别文本容器。

## <a name="create-a-kubernetes-secret"></a>创建 Kubernetes 机密 

使用 Kubectl create secret 命令访问专用容器注册表。 将替换 `<username>` 为用户名，将替换为 `<password>` 从 Azure 认知服务团队收到的凭据中提供的密码。

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>准备 YAML 配置文件

使用 YAML 配置文件在 Kubernetes 群集上简化认知服务的部署。

下面是一个示例 YAML 配置文件，用于将面部服务部署到 Azure Stack 中心：

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

在此 YAML 配置文件中，使用了用于从 Azure 容器注册表获取认知服务容器映像的机密。 机密文件用于部署容器的特定副本。 你还可以创建负载均衡器，以确保用户可以在外部访问此服务。

有关重要字段的详细信息：

| 字段 | 注释 |
| --- | --- |
| replicaNumber | 定义要创建的实例的初始副本。 以后可以在部署后对其进行缩放。 |
| ImageLocation | 指示 ACR 中特定认知服务容器映像的位置。 例如，人脸服务：`aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |步骤[创建 Azure 资源](#create-azure-resources)中所述的终结点 URL |
| ApiKey | 在[创建 Azure 资源](#create-azure-resources)中记录的订阅密钥 |
| SecretName | 在步骤中创建的机密名称[创建 Kubernetes 机密](#create-a-kubernetes-secret) |

## <a name="deploy-the-cognitive-service"></a>部署认知服务

使用以下命令部署认知服务容器：

```bash  
    Kubectl apply -f <yamlFileName>
```
使用以下命令监视部署方式： 
```bash  
    Kubectl get pod - watch
```

## <a name="configure-http-proxy-settings"></a>配置 HTTP 代理设置

辅助角色节点需要代理和 SSL。 若要配置 HTTP 代理来发出出站请求，请使用以下两个参数：

- **HTTP_PROXY** -要使用的代理，例如`https://proxy:8888`
- **HTTP_PROXY_CREDS** –例如，对代理进行身份验证所需的任何凭据 `username:password` 。

### <a name="set-up-the-proxy"></a>设置代理

1. 将 `http-proxy.conf` 文件添加到这两个位置：
    - `/etc/system/systemd/docker.service.d/`
    - `/cat/etc/environment/`

2. 验证是否可以使用认知服务团队提供的凭据登录到容器，并 `docker pull` 在以下容器中执行： 

    `docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest`

    运行：

    `docker run hello-world pull`

### <a name="ssl-interception-setup"></a>SSL 侦听设置

1. 将**https 截获**证书添加到 `/usr/local/share/ca-certificates` ，并将其更新为 `update-ca-certificates` 。 

## <a name="test-the-cognitive-service"></a>测试认知服务

访问该容器的 **/swagger**相对 URI 中的[OpenAPI 规范](https://swagger.io/docs/specification/about/)。 此规范（以前称为 Swagger 规范）描述了实例化容器支持的操作。 例如，以下 URI 提供对上一示例中实例化的情绪分析容器 OpenAPI 规范的访问：

```HTTP  
http:<External IP>:5000/swagger
```

可通过以下命令获取外部 IP 地址： 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>尝试使用 Python 来测试服务

可以通过运行一些简单的 Python 脚本，尝试在 Azure Stack 中心验证认知服务。 你的参考[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)、[面部](https://docs.microsoft.com/azure/cognitive-services/face/overview)、[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)和[语言理解](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)（LUIS）具有官方 Python 快速入门示例。

使用 Python 应用验证容器上运行的服务时，有两个需要注意的事项： 
1. 容器中的认知服务不需要用于身份验证的子键，但仍需要将任何字符串用作占位符以满足 SDK 要求。 
2. 将 base_URL 替换为实际服务终结点 IP 地址。

下面是使用面部服务 Python SDK 检测图像中的人脸的示例 Python 脚本：

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack Hub
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

[如何安装和运行语言理解（LIUS）容器](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
