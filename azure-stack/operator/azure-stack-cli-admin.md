---
title: 为 Azure Stack Hub 用户启用 Azure CLI
description: 了解如何启用跨平台命令行接口 (CLI) 来管理和部署 Azure Stack Hub 上的资源。
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: b9df823c61a9c0c54975188d15046ea4d73f9ca4
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246038"
---
# <a name="enable-azure-cli-for-azure-stack-hub-users"></a>为 Azure Stack Hub 用户启用 Azure CLI

可以向 Azure Stack Hub 的用户提供 CA 根证书，使他们能够在其开发计算机上启用 Azure CLI。 用户需使用该证书通过 CLI 管理资源。

 - 如果用户通过 Azure Stack 开发工具包 (ASDK) 外部的工作站使用 CLI，则 **Azure Stack Hub CA 根证书** 是必需的。  

 - **虚拟机 (VM) 别名终结点** 提供在部署 VM 时以单个参数形式引用映像发布者、产品/服务、SKU 和版本的别名，如“UbuntuLTS”或“Win2012Datacenter”。  

以下部分介绍如何获取这些值。

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>导出 Azure Stack Hub CA 根证书

如果使用集成系统，则无需导出 CA 根证书。 需要在 ASDK 中导出 CA 根证书。

若要以 PEM 格式导出 ASDK 根证书，请登录并运行以下脚本：

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

Azure Stack Hub 操作员应设置可公开访问的终结点来托管 VM 别名文件。 VM 别名文件是一个 JSON 文件，提供映像的公用名称。 以 Azure CLI 参数形式部署 VM 时，将使用该名称。  

向别名文件添加条目之前，请确保[从 Azure 市场下载映像](azure-stack-download-azure-marketplace-item.md)，或者已[发布自己的自定义映像](azure-stack-add-vm-image.md)。 如果发布自定义映像，请记下发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果映像来自市场，可以使用 `Get-AzureVMImage` cmdlet 查看信息。  

可以使用包含许多常见映像别名的[示例别名文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。 可以使用该文件作为起点。 将该文件托管在 CLI 客户端可以访问它的空间。 一种方法是将该文件托管在 blob 存储帐户中并与用户共享 URL：

1. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。
2. 在 Azure Stack Hub 中创建存储帐户。 完成该操作后，将创建 Blob 容器。 将访问策略设置为“公开”。  
3. 将 JSON 文件上传到新容器。 完成该操作后，可以查看 blob 的 URL。 选择 blob 名称，然后从 blob 属性中选择该 URL。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](../user/azure-stack-deploy-template-command-line.md )
- [使用 PowerShell 连接](powershell-install-az-module.md)
- [管理用户权限](azure-stack-manage-permissions.md)
