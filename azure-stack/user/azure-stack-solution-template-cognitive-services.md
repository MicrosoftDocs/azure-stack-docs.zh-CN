---
title: 将 Azure 认知服务部署到 Azure Stack Hub
description: 了解如何将 Azure 认知服务部署到 Azure Stack Hub。
author: mattbriggs
ms.topic: article
ms.date: 10/09/2020
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 4dc6ad30d54b61e05f54be4a4f1301472794db60
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697683"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>将 Azure 认知服务部署到 Azure Stack Hub

可以在 Azure Stack Hub 上使用支持容器的 Azure 认知服务。 Azure 认知服务中的容器支持可让你使用 Azure 提供的相同丰富 API。 使用容器可以灵活部署和托管 [Docker 容器](https://www.docker.com/what-container)中提供的服务。 

容器化是一种软件分发方法，其中的应用或服务（包括其依赖项和配置）打包为容器映像。 只需进行少量的修改或无需修改，即可将映像部署到容器主机。 每个容器与其他容器和基础操作系统相隔离。 系统本身仅包含运行映像所需的组件。 容器主机的占用空间比虚拟机更小。 还可以从映像中为短期任务创建容器，不再需要容器时可将其删除。

容器支持目前适用于部分 Azure 认知服务：

- **语音**
    - 语音到文本 (标准) 
    - 语音到文本 (自定义) 
    - 文本转语音（标准）
    - 文本到语音 (神经) 
- **语言**
    - 语言理解
    - 文本分析（情绪分析）
- **决策**
    - 异常检测器


> [!IMPORTANT]
> 适用于 Azure Stack Hub 的部分 Azure 认知服务目前为公共预览版。
> 预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

目前为部分 Azure 认知服务提供公共预览版的容器支持：

- **计算机视觉**
    - Read 3.0、Read 3.1
    - 空间分析（新）
- **语言**
    - 关键短语提取
    - 语言检测
    - 运行状况文本分析
- **语音**
    - 语音语言检测
    - 神经 TTS
    - 文本转语音（自定义）

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>在 Azure Stack Hub 上使用包含认知服务的容器

- **对数据进行控制**  
  允许应用用户在使用认知服务时控制其数据。 可将认知服务提供给无法向全球 Azure 或公有云发送数据的应用用户。

- **对模型更新进行控制**  
  为应用用户提供其解决方案中部署的模型的版本更新。

- **可移植的体系结构**  
  实现可移植应用体系结构的创建，以便将解决方案部署到公有云、本地私有云或边缘设备。 可将容器部署到 Azure Kubernetes 服务、Azure 容器实例或者 Azure Stack Hub 中的 Kubernetes 群集。 有关详细信息，请参阅[将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)。

- **高吞吐量与低延迟**  
   使应用用户能够根据流量高峰进行缩放，以实现高吞吐量与低延迟。 使认知服务能够在 Azure Kubernetes 服务中以接近其应用逻辑和数据的方式运行。

使用 Azure Stack Hub 在 Kubernetes 群集中连同应用容器一起部署认知服务容器，以实现高可用性和弹性缩放。 可以通过将认知服务与基于应用服务、Functions、Blob 存储、SQL 或 MySQL 数据库构建的组件相结合，来开发应用。

有关认知服务容器的更多详细信息，请参阅 [Azure 认知服务中的容器支持](/azure/cognitive-services/cognitive-services-container-support)。

## <a name="deploy-the-azure-face-api"></a>部署 Azure 人脸 API

本文将介绍如何在 Azure Stack Hub 上的 Kubernetes 群集中部署 Azure 人脸 API。 可以使用相同的方法将其他认知服务容器部署到 Azure Stack Hub Kubernetes 群集。

## <a name="prerequisites"></a>先决条件

准备工作：

1.  请求容器注册表的访问权限，以便能够从 Azure 认知服务容器注册表提取人脸容器映像。 有关详细信息，请参阅[请求访问专用容器注册表](/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)。

2.  准备 Azure Stack Hub 上的 Kubernetes 群集。 可以遵循[将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md) 一文。

## <a name="create-azure-resources"></a>创建 Azure 资源

在 Azure 上创建认知服务资源以预览“人脸”、语言理解 (LUIS) 或识别文本容器。 需要使用资源中的订阅密钥和终结点 URL 来实例化认知服务容器。

1. 在 Azure 门户中创建 Azure 资源。 若要预览“人脸”容器，必须先在 Azure 门户中创建相应的“人脸”资源。 有关详细信息，请参阅[快速入门：在 Azure 门户中创建认知服务帐户](/azure/cognitive-services/cognitive-services-apis-create-account)。

   > [!NOTE]
   >  “人脸”或“计算机视觉”资源必须使用 F0 定价层。

2. 获取 Azure 资源的终结点 URL 和订阅密钥。 创建 Azure 资源后，必须使用该资源中的订阅密钥和终结点 URL 来实例化相应的“人脸”、“LUIS”或“识别文本”容器，以进行预览。

## <a name="create-a-kubernetes-secret"></a>创建 Kubernetes 机密 

使用 Kubectl create secret 命令访问专用容器注册表。 将 `<username>` 替换为用户名，将 `<password>` 替换为从 Azure 认知服务团队收到的凭据中提供的密码。

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>准备 YAML 配置文件

使用 YAML 配置文件来简化 Kubernetes 群集上的认知服务部署。

以下 YAML 示例配置文件可将“人脸”服务部署到 Azure Stack Hub：

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

在此 YAML 配置文件中，使用了用于从 Azure 容器注册表获取认知服务容器映像的机密。 使用机密文件部署容器的特定副本。 此外，可以创建一个负载均衡器，用于确保用户可在外部访问此服务。

有关重要字段的详细信息：

| 字段 | 注释 |
| --- | --- |
| replicaNumber | 定义要创建的实例的初始副本。 可以在部署之后可进行缩放。 |
| ImageLocation | 指示 ACR 中特定认知服务容器映像的位置。 例如，人脸服务：`aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |在[创建 Azure 资源](#create-azure-resources)步骤中记下的终结点 URL |
| ApiKey | 在[创建 Azure 资源](#create-azure-resources)步骤中记下的订阅密钥 |
| SecretName | 在[创建 Kubernetes 机密](#create-a-kubernetes-secret)步骤中创建的机密名称 |

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

辅助角色节点需要代理和 SSL。 要配置 HTTP 代理以发出出站请求，请使用以下两个参数：

- **HTTP_PROXY** -要使用的代理，例如 `https://proxy:8888`
- **HTTP_PROXY_CREDS** -例如，对代理进行身份验证所需的任何凭据 `username:password` 。

### <a name="set-up-the-proxy"></a>设置代理

1. 将 `http-proxy.conf` 文件添加到两个位置：
    - `/etc/system/systemd/docker.service.d/`
    - `/cat/etc/environment/`

2. 验证是否可以使用认知服务团队提供的凭据登录到容器，并在以下容器中执行 `docker pull`： 

    `docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest`

    运行：

    `docker run hello-world pull`

### <a name="ssl-interception-setup"></a>SSL 拦截设置

1. 将“https 拦截”证书添加到 `/usr/local/share/ca-certificates`，并使用 `update-ca-certificates` 更新存储。 

## <a name="test-the-cognitive-service"></a>测试认知服务

通过该容器的 /swagger 相对 URI 访问 [OpenAPI 规范](https://swagger.io/docs/specification/about/)。 此规范（以前称为“Swagger 规范”）描述了实例化容器支持的操作。 例如，以下 URI 提供对上一示例中实例化的情绪分析容器 OpenAPI 规范的访问：

```HTTP  
http:<External IP>:5000/swagger
```

可通过以下命令获取外部 IP 地址： 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>尝试使用 Python 来测试服务

可以尝试运行一些简单的 Python 脚本来验证 Azure Stack Hub 上的认知服务。 有可供参考的关于[计算机视觉](/azure/cognitive-services/computer-vision/home)、[人脸](/azure/cognitive-services/face/overview)、[文本分析](/azure/cognitive-services/text-analytics/overview)和[语言理解](/azure/cognitive-services/luis/luis-container-howto) (LUIS) 的官方 Python 快速入门示例。

使用 Python 应用验证容器上运行的服务时，有两个需要注意的事项： 
1. 容器中的认知服务不需要使用子密钥进行身份验证，但仍然需要输入任意字符串作为占位符，这样才能满足 SDK 的要求。 
2. 将 base_URL 替换为实际的服务终结点 IP 地址。

以下示例 Python 脚本使用“人脸”服务 Python SDK 来检测和定格图像中的人脸：

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

[如何安装和运行计算机视觉 API 容器。](/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[如何安装和运行人脸 API 容器](/azure/cognitive-services/face/face-how-to-install-containers)

[如何安装和运行文本分析 API 容器](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[如何安装和运行语言理解 (LIUS) 容器](/azure/cognitive-services/luis/luis-container-howto)
