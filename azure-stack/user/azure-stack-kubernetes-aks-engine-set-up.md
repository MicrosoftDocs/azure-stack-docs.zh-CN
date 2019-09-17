---
title: 设置 Azure Stack 上的 AKS 引擎的先决条件 |Microsoft Docs
description: 建立在 Azure Stack 上运行 ASK Engine 的要求。
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: a65449d39f114bd0ff5eb859bd49ec36d4bb4281
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019223"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>在 Azure Stack 上设置 AKS 引擎的先决条件

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

您可以在您的环境中的 VM 上安装 AKS 引擎，也可以在任何客户端计算机上安装 Azure Stack 资源管理器终结点。 在运行该引擎之前，你需要准备好以下各项：你的订阅中可用的 AKS 基本 Ubuntu 服务器和 Linux 自定义脚本扩展、已分配给参与者角色的服务主体标识，以及一个私钥/公钥对用于访问 Ubuntu 服务器的 SSH。 此外，如果使用的是 Azure Stack 开发工具包，则需要让计算机信任适当的证书。

如果有必备组件，可以开始[定义群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)。

## <a name="prerequisites-for-the-aks-engine"></a>AKS 引擎的先决条件

若要使用 AKS 引擎，需要提供以下资源。 请记住，AKS 引擎旨在供 Azure Stack 的租户用于将 Kubernetes 群集部署到其租户订阅。 唯一需要参与 Azure Stack 运算符的部分是下载 marketplace 项和创建服务主体标识。 可以在下表中找到详细信息。

| 先决条件 | 描述 | 必填 | 说明 |
| --- | --- | --- | --- |
| Linux 自定义脚本扩展 | Linux 自定义脚本扩展2。0<br>提供适用于 Linux 的自定义脚本 2.0<br>版本：2.0.6 （或最新版本）<br>器Microsoft Corp | 必填 | 如果订阅中没有此项，请与云操作员联系。 |
| AKS 基本 Ubuntu 映像 | AKS 基础映像<br>产品/服务： aks<br>版本：2019.07.30 （或更高版本）<br>发布者： microsoft-aks<br>SKU： aks-1604-201907 | 必填 | 如果订阅中没有此项，请与云操作员联系。 有关版本依赖关系的详细信息，请参阅[将引擎与基本映像版本相匹配](#matching-engine-to-base-image-version)。 |
| Azure Stack 订阅 | 通过订阅访问 Azure Stack 中的产品/服务。 产品/服务包含可供你使用的服务。 | 必填 | 若要能够在 Azure Stack 部署任何租户工作负荷，需要首先获取[Azure Stack 订阅](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services)。 |
| 服务主体标识（SPN） |  需要通过 Azure 资源管理器部署或配置资源的应用程序必须以服务主体来表示。 | 必填 | 你可能需要联系你的 Azure Stack 操作员获取此项。  有关说明，请参阅[使用应用标识访问资源](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) |
| （SPN）分配的**参与者**角色 | 若要允许应用程序使用其服务主体访问订阅中的资源，你必须将服务主体分配给特定资源的角色。 | 必填 | 有关说明，请参阅[分配角色](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) |
| 资源组 | 资源组是用于保存 Azure 解决方案相关资源的容器。 如果未指定现有的资源组，则该工具将为你创建一个。 | 可选 | [使用 Azure 门户管理 Azure 资源管理器资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| 私有公钥 | 若要在托管 web 应用的 Azure Stack 实例中使用从开发计算机到服务器 VM 的开放 SSH 连接，需创建安全外壳（SSH）公钥和私钥对。 | 必填 | 有关如何生成密钥的说明，请参阅 [SSH 密钥生成](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key)。|

> [!Note]  
> 还可以通过[Azure CLI Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2)或[Azure Stack POWERSHELL](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install)来创建 AKS 引擎的必备组件。

## <a name="matching-engine-to-base-image-version"></a>使引擎与基本映像版本匹配

AKS 引擎使用生成的映像**AKS 基本映像**。 任何 AKS 引擎版本都依赖于 Azure Stack 操作员 Azure Stack 提供的特定映像版本。 可以在[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)中找到一个表，其中列出了 AKS 引擎版本和相应支持的 Kubernetes 版本。 例如，AKS 引擎版本`v0.40.0`依赖于 AKS 基础映像的版本。 `2019.08.21` 请要求 Azure Stack 操作员将特定映像版本从 Azure Marketplace 下载到 Azure Stack Marketplace。

如果 Azure Stack Marketplace 中的映像不可用，则会触发此错误。 例如，如果你当前使用的是 AKS engine 版本 v 0.39.1，而 AKS 基本映像`2019.08.09`版本不可用，则在运行 AKS 引擎时，你将看到以下错误： 

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
> [在 Azure Stack 的 Windows 上部署 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [在 Azure Stack 中的 Linux 上部署 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-linux.md)