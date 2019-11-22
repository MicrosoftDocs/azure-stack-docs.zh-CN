---
title: 将 Linux 映像添加到 Azure Stack Marketplace |Microsoft Docs
description: 了解如何将 Linux 映像添加到 Azure Stack Marketplace。
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 208e632634c59be0338c70020e7fc0fdae846797
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299010"
---
# <a name="add-linux-images-to-azure-stack-marketplace"></a>将 Linux 映像添加到 Azure Stack Marketplace

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过将基于 Linux 的映像添加到 Azure Stack Marketplace，在 Azure Stack 上部署 Linux 虚拟机（Vm）。 向 Azure Stack 添加 Linux 映像的最简单方式是使用“市场管理”。 这些映像已针对 Azure Stack 准备就绪并且进行了兼容性测试。

## <a name="marketplace-management"></a>市场管理

若要从 Azure Marketplace 下载 Linux 映像，请参阅[将 Marketplace 项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)。 选择要在 Azure Stack 上提供给用户的 Linux 映像。

这些映像频繁更新，因此请经常查看“市场管理”以保持最新。

## <a name="prepare-your-own-image"></a>准备自己的映像

请尽可能通过“市场管理”下载映像。 这些映像已准备就绪并针对 Azure Stack 进行了测试。

### <a name="azure-linux-agent"></a>Azure Linux 代理

需要 Azure Linux 代理（通常称为**WALinuxAgent**或**WALinuxAgent**），而不是所有版本的代理都 Azure Stack。 Azure Stack 上不支持2.2.21 和2.2.34 （含）之间的版本。 若要使用 2.2.35 以上的最新代理版本，请应用 1901 修补程序/1902 修补程序，或者将 Azure Stack 更新到 1903 版（或更高版本）。 请注意，Azure Stack 版本超过1910，则支持[cloud init](https://cloud-init.io/) 。

| Azure Stack 内部版本 | Azure Linux 代理内部版本 |
| ------------- | ------------- |
| 1.1901.0.99 或更低版本 | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 或更高版本 |
| 1.1902.2.73  | 2.2.35 或更高版本 |
| 1.1903.0.35  | 2.2.35 或更高版本 |
| 1903 之后的版本 | 2.2.35 或更高版本 |
| 不支持 | 2.2.21-2.2.34 |
| 1910之后的版本 | 所有 Azure WALA 代理版本|

可以按照以下说明准备自己的 Linux 映像：

* [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES 和 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-init

Azure Stack 版本超过1910，则支持[Cloud init](https://cloud-init.io/) 。 若要使用 cloud init 自定义 Linux VM，可以使用以下 PowerShell 说明： 

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

将该文件上传到 Azure 存储帐户、Azure Stack 的存储帐户，或者 Azure Stack Linux VM 可访问的 GitHub 存储库。
目前，仅在 REST、Powershell 和 CLI 上支持使用 cloud init 进行 VM 部署，并且在 Azure Stack 上没有关联的门户 UI。

可以按照[这些](../user/azure-stack-quick-create-vm-linux-powershell.md)说明使用 Powershell 创建 Linux VM，但请确保引用 cloud-init.txt 作为 `-CustomData` 标志的一部分：

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>将映像添加到 Marketplace

按照[将映像添加到市场](azure-stack-add-vm-image.md)进行操作。 请确保 `OSType` 参数已设置为 `Linux`。

将映像添加到 Marketplace 后，会创建 Marketplace 项，用户可以部署 Linux VM。

## <a name="next-steps"></a>后续步骤

* [将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack 市场概述](azure-stack-marketplace.md)
