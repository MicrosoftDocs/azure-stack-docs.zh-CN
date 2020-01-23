---
title: 为 Azure Stack 中心用户启用 Azure CLI |Microsoft Docs
description: 了解如何启用跨平台命令行接口（CLI）来管理和部署 Azure Stack 集线器上的资源。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 333fd0eeb3f3e7162069d98c8f45a94228af5032
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534033"
---
# <a name="enable-azure-cli-for-azure-stack-hub-users"></a>为 Azure Stack 中心用户启用 Azure CLI

你可以向 Azure Stack 中心的用户提供 CA 根证书，以便可以在其开发计算机上启用 Azure CLI。 你的用户需要该证书以通过 CLI 管理资源。

 - 如果用户使用 Azure Stack 开发工具包（ASDK）之外的工作站中的 CLI，则需要**Azure Stack 中心 CA 根证书**。  

 - **虚拟机（VM）别名终结点**提供别名，如 "UbuntuLTS" 或 "Win2012Datacenter"，在部署 vm 时，它们引用映像发布者、产品/服务、SKU 和版本作为单一参数。  

以下部分介绍如何获取这些值。

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>导出 Azure Stack 中心 CA 根证书

如果使用的是集成系统，则无需导出 CA 根证书。 需要在 ASDK 上导出 CA 根证书。

若要导出 PEM 格式的 ASDK 根证书，请登录并运行以下脚本：

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-vm-aliases-endpoint"></a>设置 VM 别名终结点

Azure Stack 中心操作员应该设置一个可公开访问的终结点，该终结点承载 VM 别名文件。 VM 别名文件是一个 JSON 文件，它提供映像的公用名称。 将 VM 作为 Azure CLI 参数部署时，将使用该名称。  

在将条目添加到别名文件之前，请确保[从 Azure Marketplace 下载映像](azure-stack-download-azure-marketplace-item.md)或已[发布自定义映像](azure-stack-add-vm-image.md)。 如果发布自定义映像，请记下在发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果它是 marketplace 中的映像，则可以使用 `Get-AzureVMImage` cmdlet 来查看信息。  

提供了包含许多常见映像别名的[示例别名文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。 可以将其用作起点。 将此文件托管在 CLI 客户端可以访问的空间中。 一种方法是将文件托管在 blob 存储帐户中，并与用户共享 URL：

1. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。
2. 在 Azure Stack Hub 中创建存储帐户。 完成此操作后，创建一个 blob 容器。 将访问策略设置为 "公共"。  
3. 将 JSON 文件上传到新容器。 完成此操作后，可以查看 blob 的 URL。 选择 "blob 名称"，然后从 "blob 属性" 中选择 "URL"。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](../user/azure-stack-deploy-template-command-line.md )
- [使用 PowerShell 连接](azure-stack-powershell-install.md)
- [管理用户权限](azure-stack-manage-permissions.md)
