---
title: 将 Linux 映像添加到 Azure Stack 集线器 Marketplace |Microsoft Docs
description: 了解如何将 Linux 映像添加到 Azure Stack 集线器 Marketplace。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 93b69057d70d12dda92186819731ee707032601a
ms.sourcegitcommit: ea90ddcae07857ee4f77670891ac7814da8bdc17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2020
ms.locfileid: "76750467"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>将 Linux 映像添加到 Azure Stack 中心市场

可以通过将基于 Linux 的映像添加到 Azure Stack 中心市场，在 Azure Stack 集线器上部署 Linux 虚拟机（Vm）。 将 Linux 映像添加到 Azure Stack 集线器的最简单方法是通过 Marketplace 管理。 这些映像已准备好并经过测试，可与 Azure Stack 中心兼容。

## <a name="marketplace-management"></a>Marketplace 管理

若要从 Azure Marketplace 下载 Linux 映像，请参阅将[Marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)。 选择要在 Azure Stack 中心为用户提供的 Linux 映像。

经常会对这些映像进行更新，因此请经常查看 Marketplace 管理以保持最新状态。

## <a name="prepare-your-own-image"></a>准备自己的映像

请尽可能通过“市场管理”下载映像。 这些映像已准备好并针对 Azure Stack 集线器进行测试。

### <a name="azure-linux-agent"></a>Azure Linux 代理

需要 Azure Linux 代理（通常称为**WALinuxAgent**或**WALinuxAgent**），而不是所有版本的代理都在 Azure Stack 集线器上工作。 Azure Stack 集线器上不支持2.2.21 和2.2.34 （含）之间的版本。 若要在2.2.35 上使用最新的代理版本，请应用1901修补程序/1902 修补程序，或将 Azure Stack 集线器更新到1903版本（或更高版本）。 请注意，1910以外的 Azure Stack 集线器版本支持[云初始化](https://cloud-init.io/)。

| Azure Stack 中心生成 | Azure Linux 代理版本 |
| ------------- | ------------- |
| 1.1901.0.99 或更早版本 | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 或更高版本 |
| 1.1902.2.73  | 2.2.35 或更高版本 |
| 1.1903.0.35  | 2.2.35 或更高版本 |
| 1903之后的版本 | 2.2.35 或更高版本 |
| 不支持 | 2.2.21-2.2.34 |
| 1910之后的版本 | 所有 Azure WALA 代理版本|

可以使用以下说明准备自己的 Linux 映像：

* [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES 和 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-init

1910以外的 Azure Stack 集线器版本支持[Cloud init](https://cloud-init.io/) 。 若要使用 cloud init 自定义 Linux VM，可以使用以下 PowerShell 说明。

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>步骤1：创建 cloud-init.txt 文件与你的云配置

创建名为 cloud-init.txt 的文件并粘贴以下云配置：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-the-cloud-inittxt-during-the-linux-vm-deployment"></a>步骤2：在 Linux VM 部署期间引用 cloud-init.txt

将文件上传到 Azure 存储帐户、Azure Stack 中心存储帐户，或 Azure Stack 中心 Linux VM 可访问的 GitHub 存储库。
目前，仅在 REST、Powershell 和 CLI 上支持使用 cloud init 进行 VM 部署，并且在 Azure Stack 中心没有关联的门户 UI。

可以按照[这些](../user/azure-stack-quick-create-vm-linux-powershell.md)说明使用 Powershell 创建 Linux VM，但请确保引用 cloud-init.txt 作为 `-CustomData` 标志的一部分：

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>将映像添加到 Marketplace

按照[将映像添加到 Marketplace](azure-stack-add-vm-image.md)。 请确保将 `OSType` 参数设置为 `Linux`。

将映像添加到 Marketplace 后，会创建 Marketplace 项，用户可以部署 Linux VM。

## <a name="next-steps"></a>后续步骤

* [将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack 中心市场概述](azure-stack-marketplace.md)
