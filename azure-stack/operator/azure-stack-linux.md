---
title: 将 Linux 映像添加到 Azure Stack Hub 市场
description: 了解如何将 Linux 映像添加到 Azure Stack Hub 市场。
author: sethmanheim
ms.topic: article
ms.date: 05/07/2020
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 11/16/2019
ms.openlocfilehash: 32cdb1ec98f9825a63b5acfa68e4c8e3f43089a7
ms.sourcegitcommit: 9894804f31527234d43f4a93a9b7c106c8540435
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967720"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>将 Linux 映像添加到 Azure Stack Hub 市场

可以通过将基于 Linux 的映像添加到 Azure Stack Hub 市场，在 Azure Stack Hub 上部署 Linux 虚拟机 (VM)。 将 Linux 映像添加到 Azure Stack 集线器的最简单方法是通过 marketplace 管理。 这些映像已准备好，并已针对与 Azure Stack Hub 的兼容性进行测试。

## <a name="marketplace-management"></a>市场管理

若要从 Azure 市场下载 Linux 映像，请参阅[将市场项从 Azure 下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。 选择要在 Azure Stack Hub 上提供给用户的 Linux 映像。

经常对这些映像进行更新，因此请经常回来查看以保持最新状态。

## <a name="prepare-your-own-image"></a>准备自己的映像

只要有可能，下载通过 marketplace 管理提供的映像。 这些映像已针对 Azure Stack Hub 进行了准备和测试。

### <a name="azure-linux-agent"></a>Azure Linux 代理

Azure Linux 代理（通常称为 **WALinuxAgent** 或 **walinuxagent**）是必需的，并非所有代理版本都可以在 Azure Stack Hub 上正常工作。 Azure Stack Hub 不支持 2.2.21 和 2.2.34（含）之间的版本。 若要使用 2.2.35 以上的最新代理版本，请应用 1901 修补程序/1902 修补程序，或者将 Azure Stack Hub 更新到 1903 版（或更高版本）。 请注意，超过 1910 的 Azure Stack Hub 版本支持 [cloud-init](https://cloud-init.io/)。

| Azure Stack Hub 内部版本 | Azure Linux 代理内部版本 |
| ------------- | ------------- |
| 1.1901.0.99 或更低版本 | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 或更高版本 |
| 1.1902.2.73  | 2.2.35 或更高版本 |
| 1.1903.0.35  | 2.2.35 或更高版本 |
| 1903 之后的版本 | 2.2.35 或更高版本 |
| 不支持 | 2.2.21-2.2.34 |
| 1910 之后的版本 | 所有 Azure WALA 代理版本|

可以按照以下说明准备自己的 Linux 映像：

* [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES 和 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-init

超过 1910 的 Azure Stack Hub 版本支持 [cloud-init](https://cloud-init.io/)。 若要使用 cloud init 自定义 Linux VM，可以使用以下 PowerShell 说明。

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>步骤 1：使用你的 cloud-config 创建 cloud-init.txt 文件

创建名为“cloud-init.txt”的文件并粘贴以下云配置：

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
  
### <a name="step-2-reference-cloud-inittxt-during-the-linux-vm-deployment"></a>步骤2：在 Linux VM 部署期间引用 cloud-init.txt

将该文件上传到 Azure 存储帐户、Azure Stack Hub 存储帐户，或者 Azure Stack Hub Linux VM 可访问的 GitHub 存储库。
目前，仅在 REST、Powershell 和 CLI 上支持使用 cloud init 进行 VM 部署，并且在 Azure Stack 集线器上没有关联的门户 UI。

可以按照[这些说明](../user/azure-stack-quick-create-vm-linux-powershell.md)使用 Powershell 创建 Linux VM，但请务必引用 cloud-init.txt 作为`-CustomData`标志的一部分：

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>将映像添加到市场

按照[将映像添加到市场](azure-stack-add-vm-image.md)进行操作。 请确保 `OSType` 参数已设置为 `Linux`。

将映像添加到市场后，便会创建市场项，用户就可以部署 Linux VM 了。

## <a name="next-steps"></a>后续步骤

* [将市场项从 Azure 下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack Hub 市场概述](azure-stack-marketplace.md)
