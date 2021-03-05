---
title: 在 Azure Stack 集线器上轮换 Kubernetes 证书
description: 了解如何在 Azure Stack Hub 上轮换 Kubernetes 证书。
author: mattbriggs
ms.topic: how-to
ms.date: 3/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/1/2021
ms.openlocfilehash: fa46de76f59e664fb3dd4a1c771ad7b835f97ca4
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102233835"
---
# <a name="rotate-kubernetes-certificates-on-azure-stack-hub"></a>在 Azure Stack 集线器上轮换 Kubernetes 证书

本文档提供有关如何在现有的 AKS 引擎群集上轮替证书的指导，以及使用采用 `aks-engine rotate-certs` 作为工具的建议。

> [!IMPORTANT]  
> 此功能目前处于公开预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本指南假定你已使用 AKS 引擎部署了群集，并且群集处于正常状态。
## <a name="planning-for-certificate-rotation"></a>规划证书轮换

考虑使用此功能时，请注意在更新、验证和重启步骤中将无法使用 Kubernetes 控制平面。 请相应地计划此维护操作。 此外，在尝试生产之前，请计划在具有相同配置的过渡环境中执行此操作。

尝试此操作之前，请查看以下注意事项：

-  你将需要访问 `apimodel.json`) 命令或生成的 API 模型 (`aks-engine deploy` `aks-engine generate` 。 默认情况下，此文件放置在一个相对目录中，例如 `_output/<clustername>/` 。
-  `aks-engine rotate-certs`操作导致 API 服务器停机。
-  `aks-engine rotate-certs` 需要符合群集的当前状态的 API 模型。 `aks-engine rotate-certs` 在群集节点上执行远程命令，并使用 API 模型信息建立安全的 SSH 连接。 `aks-engine rotate-certs` 还依赖于原始部署来命名某些资源 `aks-engine` ，例如，vm 必须遵循提供的命名 `aks-engine` 。
-  `aks-engine rotate-certs` 在证书轮换期间依赖于与群集控制平面的工作连接：
    - 验证过程的每个步骤。
    - 重新启动/重新创建群集资源，例如 **kube** pod 和服务帐户令牌。

    如果要将 VNet 中已关闭的群集的证书轮换到外部访问，则必须从对 `aks-engine rotate-certs` 控制平面具有网络访问权限的主机 vm （例如，驻留在与主 vm 相同的 VNet 中的 JUMPBOX vm）中运行。

- 如果要 `aks-engine rotate-certs` 在生产环境中使用，建议在生成了相同规范的群集上暂存证书轮换测试。 也就是说，在执行证书轮换之前，将用相同的群集配置、同一版本的 AKS 引擎命令行工具和已启用的加载项集作为生产群集来构建群集。 AKS 引擎支持不同的群集配置，而 AKS 引擎团队运行的端到端测试的程度几乎不能涵盖每个可能的配置。 因此，建议在过渡环境中确保特定群集配置的工作原理， `aks-engine rotate-certs` 然后再在生产群集上尝试该操作。
-  `aks-engine rotate-certs` 不 **保证向** 后兼容。 如果使用 aks 版本0.60 部署了，则应首选使用版本0.60 执行证书轮换过程。
-  此时不支持从 Key Vault 提取一组新证书。
- 使用可靠的网络连接。 `aks-engine rotate-certs` 需要执行多个远程命令，这可能会导致潜在的故障，在大多数情况下，如果与群集节点的连接不可靠。 `aks-engine rotate-certs`从在目标 Azure Stack 戳记上运行的 VM 运行可减少暂时性问题的出现。

## <a name="parameters"></a>参数

| 参数           | 必需 | 描述 |
| --- | --- | --- |
| --api-model             | 是          | API 模型的相对路径 (分类定义声明所需的群集配置) 。       |
| --ssh 主机              | 是          | 完全限定的域名 (FQDN) 或 IP 地址，可以访问群集中的所有节点。                            |
| --linux-ssh-私钥 | 是          | 用于访问群集的 Linux 节点的有效专用 SSH 密钥的路径。                                        |
| --location              | 是          | 部署群集的 Azure 位置。                                                               |
| --订阅 id       | 是          | 部署了群集的 Azure 订阅。                                                     |
| --resource-group        | 是          | 部署群集基础的 Azure 资源组。                                                   |
| --客户端 id             | 依赖      | 服务主体客户端 ID。 如果身份验证方法设置为 client_secret 或 client_certificate，则为必需。 |
| --客户端-密码         | 依赖      | 服务主体客户端机密。 如果身份验证方法设置为 client_secret，则为必需。                   |
| --azure-env             | 依赖      | 目标云名称。 如果目标云为 AzureCloud，则为可选。                                              |
| --证书-配置文件   | 否           | 包含新证书集的 JSON 文件的相对路径。                                        |
| --force                 | 否           | 即使 API 服务器没有响应，也强制执行。                                                       |

## <a name="simple-steps-to-rotate-certificates"></a>循环证书的简单步骤

阅读所有 [要求](https://github.com/Azure/aks-engine/blob/master/docs/topics/rotate-certs.md#pre-requirements)后， `aks-engine rotate-certs` 使用适当的参数运行：

```bash  
./bin/aks-engine rotate-certs \
  --location <resource-group-location> \
  --api-model <generated-apimodel.json> \
  --linux-ssh-private-key <private-SSH-key> \
  --ssh-host <apiserver-URI> \
  --resource-group <resource-group-name> \
  --client-id <service-principal-id> \
  --client-secret <service-principal-secret> \
  --subscription-id <subscription-id> \
  --azure-env <cloud-name>
```

例如：

```bash  
./bin/aks-engine rotate-certs \
  --location "westus2" \
  --api-model "_output/my-cluster/apimodel.json" \
  --linux-ssh-private-key "~/.ssh/id_rsa" \
  --ssh-host "my-cluster.westus2.cloudapp.azure.com"\
  --resource-group "my-cluster" \
  --client-id "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --client-secret "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --subscription-id "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --azure-env "AzureStackCloud" # optional if targeting AzureCloud
```

## <a name="troubleshooting"></a>疑难解答

如果由于故障或暂时性问题（例如，网络连接）导致证书轮换过程在完成之前暂停，则可以安全地 `aks-engine rotate-certs` 使用标志重新运行 `--force` 。

另请注意， `aks-engine rotate-certs` 将文件 `/var/log/azure/rotate-certs.log` (Linux) 和 `c:\\k\\rotate-certs.log` (Windows) 中的每个步骤的输出记录到日志中。

有关运行此操作或进行进一步自定义时所发生情况的详细信息，请参阅 [后台的](https://github.com/Azure/aks-engine/blob/master/docs/topics/rotate-certs.md#under-the-hood)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
