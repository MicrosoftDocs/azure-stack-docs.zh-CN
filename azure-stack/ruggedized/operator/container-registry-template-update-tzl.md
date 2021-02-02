---
title: 更新 Azure Stack 集线器中的容器注册表 |Microsoft Docs
titleSuffix: Azure Stack
description: 了解如何在 Azure Stack Hub 中更新容器注册表。
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
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: b627b3f34b585189492cb3c3c3dfdef6965d5c61
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247398"
---
# <a name="update-the-container-registry-in-azure-stack-hub"></a>更新 Azure Stack 集线器中的容器注册表

Azure Stack 集线器用户可以使用下面的说明将其容器注册表部署更新为较新的 AKS 基本映像 SKU。 容器注册表模板 VM 和服务无状态，因为所有状态和容器映像都存储在 blob 存储中。 更新简单，只是使用最新版本的 AKS 基本映像 VHD 和重构 DNS 将容器注册表模板部署到新 VM。 为新的和新的容器注册表模板 Vm 更新 DNS 值的操作将导致在值传播时出现间歇性的注册表连接。

## <a name="prerequisites"></a>必备条件

### <a name="operator"></a>运算符

1.  将最新的 AKS 基本映像与 Azure Stack Marketplace 联合。 AKS 基本映像会按月进行更新。

> ![显示 "从 Azure 添加" 页的屏幕截图，其中显示了 "AKS Base Ubu" 的搜索结果。](./media/container-registry-template-updating-tzl/image1.png)

### <a name="user"></a>用户

1.  通过浏览到资源组中的部署记录并选择 " **输入**"，查看用于部署容器注册表模板的 AKS 基本映像的 SKU。

    ![显示 "输入" 页的屏幕截图。](./media/container-registry-template-updating-tzl/image2.png)

2.  使用 **VMImageSku** 函数确定是否有更高版本的 AKS 基本映像可用，要求使用 `Import-Module .\pre-reqs.ps1` 容器注册表模板脚本。

    ```powershell  
    PS C:\azurestack-galler-master\registry\Scripts> Get-VMImageSku -Location Shanghai
    
    Skus                  
    ----                  
    aks-ubuntu-1604-201909
    aks-ubuntu-1604-201910 
    ```

## <a name="parameters-required"></a>必需的参数

| 参数 | 详细信息 |
| --- | --- |
| 用户名 | 提供登录到 VM 的用户名。 |
| SSH 公钥 | 提供使用 SSH 协议向 VM 进行身份验证的 SSH 公钥。 |
| 大小 | 选择要部署的 VM 的大小。 |
| 公共 IP 地址 | 为此 VM 指定 (动态/静态) 的 IP 地址名称和类型。 |
| 域名标签 | 指定注册表的 DNS 前缀。 此整个 FQDN 应与为注册表创建的 PFX 证书的 CN 值匹配。 |
| 副本 | 指定要启动的容器副本数。 |
| 映像 SKU | 指定要用于部署的映像 SKU。 **VMImageSku** PowerShell cmdlet 列出了 AKS 基本映像的可用 sku。 |
| 服务主体客户端 ID | 指定在以前的部署中使用的服务主体 (SPN) 应用 ID。 |
| 服务主体密码/确认密码 | 指定在以前的部署中使用的 SPN 应用 ID 密码。 |
| 现有的扩展存储帐户资源 ID | 指定在以前的部署中使用的存储帐户资源 ID。 |
| 现有后端 blob 容器 | 指定在以前的部署中使用的 blob 容器名称。 |
| PFX 证书 Key Vault 资源 ID | 指定在以前的部署中使用的 Microsoft Azure Key Vault 资源 ID。 |
| PFX 证书 Key Vault 机密 URL | 指定在以前的部署中使用的证书 URL。 |
| PFX 证书指纹 | 指定在以前的部署中使用的证书指纹。 |

## <a name="installation"></a>安装

1.  将容器注册表模板的新实例安装到新的资源组。

    ![屏幕截图，显示 "创建容器注册表模板-基础知识" 页。](./media/container-registry-template-updating-tzl/image3.png)

2.  指定脚本中的最新 SKU 输出， `Get-VMImage` 并使用虚拟机配置中初始安装的唯一 **dnsname** 参数，并使用与初始安装相同的服务主体和密码。

    ![显示 "创建容器注册表模板-虚拟机配置" 页面的屏幕截图。](./media/container-registry-template-updating-tzl/image4.png)

3.  使用与存储和 Key Vault 配置的初始安装相同的存储和 Key Vault 参数。

    ![显示 "创建容器注册表模板-存储和 Key Vault 配置" 页的屏幕截图。](./media/container-registry-template-updating-tzl/image5.png)

1.  部署新容器注册表模板后，导航到初始资源组，并选择公共 IP 地址资源。

    ![屏幕截图，显示公共 I P 地址资源的列表。](./media/container-registry-template-updating-tzl/image6.png)

1.  在公共 IP 地址资源中，导航到 "配置" 并修改 "DNS 名称" 标签，使其可用于新部署的资源。 请注意，在修改 DNS 名称标签并选择 " **将调用保存** 到容器后，将开始失败"。

    ![显示已选择 "静态" 的 "公共 I P 地址" 资源页的屏幕截图。](./media/container-registry-template-updating-tzl/image7.png)
    
    ![屏幕截图显示了 "公共 I P address" 资源页，该页面的名称为 N S (可选) "突出显示。](./media/container-registry-template-updating-tzl/image8.png)

2.  导航到新资源组，该资源组用于部署容器注册表模板的新实例，选择公共 IP 资源，配置，并将 DNS 名称标签更新为在此示例中使用的正确名称， `myreg` 然后选择 " **保存**"。

    ![屏幕截图，显示 "公共 I P address" 资源页，并输入原始的 "D N S" 名称标签。](./media/container-registry-template-updating-tzl/image9.png)
    
    ![容器注册表模板](./media/container-registry-template-updating-tzl/image10.png)

3.  在接下来的30分钟内，将会在传播 DNS 记录时对容器注册表进行间歇访问。 通过登录到 Docker 注册表并请求/推送映像来验证连接。

## <a name="next-steps"></a>后续步骤

[Azure Stack 市场概述](../../operator/azure-stack-marketplace.md)
