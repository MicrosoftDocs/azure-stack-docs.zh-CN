---
title: 在 Azure Stack Hub 上设置 AKS 引擎的先决条件 |Microsoft Docs
description: 建立在 Azure Stack 集线器上运行 ASK Engine 的要求。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 1516b07cc491ca365f5bc87ad584960818bb537d
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879608"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>设置 Azure Stack 集线器上的 AKS 引擎的先决条件

您可以在您的环境中的 VM 上安装 AKS 引擎，也可以在任何客户端计算机上安装 Azure Stack 中心资源管理器终结点。 在运行该引擎之前，你需要准备好以下各项：你的订阅中提供的 AKS 基本 Ubuntu 服务器和 Linux 自定义脚本扩展、已分配给参与者角色的服务主体标识，以及用于 SSH 访问 Ubuntu 服务器的私钥/公钥对。 此外，如果使用的是 Azure Stack 开发工具包，则需要让计算机信任适当的证书。

如果有必备组件，可以开始[定义群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)。

如果你是 Azure Stack 中心的云操作员，并且想要提供 AKS 引擎，请按照[将 AKS 引擎添加到 Azure Stack 中心市场](../operator/azure-stack-aks-engine.md)中的说明进行操作。

## <a name="prerequisites-for-the-aks-engine"></a>AKS 引擎的先决条件

若要使用 AKS 引擎，需要提供以下资源。 请记住，AKS 引擎旨在供 Azure Stack 集线器的租户用于将 Kubernetes 群集部署到其租户订阅。 唯一需要 Azure Stack 中心操作员参与的部分是下载 Marketplace 项和创建服务主体标识。 可以在下表中找到详细信息。

你的云操作员需要准备好以下各项。

| 先决条件 | Description | 需要 | Instructions |
| --- | --- | --- | --- | --- |
| Linux 自定义脚本扩展 | Linux 自定义脚本扩展2。0<br>产品/服务：适用于 Linux 2.0 的自定义脚本<br>版本：2.0.6 （或最新版本）<br>发布者： Microsoft Corp | 需要 | 如果订阅中没有此项，请与云操作员联系。 |
| AKS 基本 Ubuntu 映像 | AKS 基础映像<br>产品/服务： aks<br> 2019.10.24 （或更高版本）<br>发布者： microsoft-aks<br>SKU： aks-1604-201910 | 需要 | 如果订阅中没有此项，请与云操作员联系。 有关版本依赖关系的详细信息，请参阅[将引擎与基本映像版本相匹配](#matching-engine-to-base-image-version)。<br> 如果你是 Azure Stack 中心的云操作员，并且想要提供 AKS 引擎，请按照[将 AKS 引擎添加到 Azure Stack 中心市场](../operator/azure-stack-aks-engine.md)中的说明进行操作。 |
| 服务主体标识（SPN） |  需要通过 Azure 资源管理器部署或配置资源的应用程序必须由服务主体表示。 | 需要 | 对于此项目，可能需要联系 Azure Stack 中心操作员。  有关说明，请参阅[使用应用标识访问资源](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) |
| （SPN）分配的**参与者**角色 | 若要允许应用程序使用其服务主体访问订阅中的资源，你必须将服务主体分配给特定资源的角色。 | 需要 | 有关说明，请参阅[分配角色](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) |

可以设置以下项。

| 先决条件 | Description | 需要 | Instructions |
| --- | --- | --- | --- |
| Azure Stack 中心订阅 | 通过订阅访问 Azure Stack 中心提供的产品/服务。 产品/服务包含可供你使用的服务。 | 需要 | 若要能够在 Azure Stack 中心部署任何租户工作负荷，需要首先获取[Azure Stack 中心订阅](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services)。 |
| 资源组 | 资源组是用于保存 Azure 解决方案相关资源的容器。 如果未指定现有的资源组，则该工具将为你创建一个。 | 可选 | [使用 Azure 门户管理 Azure 资源管理器资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| 私有公钥 | 若要在托管 web 应用的 Azure Stack 中心实例中使用从开发计算机到服务器 VM 的开放 SSH 连接，需创建安全外壳（SSH）公钥和私钥对。 | 需要 | 有关生成密钥的说明，请参阅[SSH 密钥生成](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key)。|


> [!Note]  
> 还可以通过[Azure CLI Azure Stack 中心](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2)或[Azure Stack 集线器 POWERSHELL](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install)来创建 AKS 引擎的必备组件。

## <a name="matching-engine-to-base-image-version"></a>使引擎与基本映像版本匹配

AKS 引擎使用生成的映像**AKS 基本映像**。 任何 AKS 引擎版本都依赖于 Azure Stack 中心操作员在 Azure Stack 中心提供的特定映像版本。 可以在[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)中找到一个表，其中列出了 AKS 引擎版本和相应支持的 Kubernetes 版本。 例如，AKS 引擎版本 `v0.43.0` 依赖于 AKS 基本映像的版本 `2019.10.24`。 请 Azure Stack 中心操作员将特定映像版本从 Azure Marketplace 下载到 Azure Stack 中心市场。

如果 Azure Stack 中心 Marketplace 中的映像不可用，则会触发此错误。 例如，如果你当前使用的是 AKS engine 版本 v 0.43.0 和 AKS 基本映像版本 `2019.10.24` 不可用，则在运行 AKS 引擎时，你将看到以下错误： 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

可以通过运行以下命令来检查 AKS 引擎的当前版本：

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure Stack 集线器中的 Windows 上部署 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [在 Azure Stack 集线器中的 Linux 上部署 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-linux.md)