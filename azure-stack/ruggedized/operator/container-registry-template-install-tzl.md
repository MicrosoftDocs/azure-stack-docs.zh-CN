---
title: 添加容器注册表-Azure Stack 集线器 |Microsoft Docs
titleSuffix: Azure Stack
description: 了解如何将容器注册表添加到 Azure Stack 中心市场 (耐用) 。
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
ms.openlocfilehash: b94ae36daf6cd0ed69ded630a1cc1a7c96be80b2
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247517"
---
# <a name="add-a-container-registry-to-azure-stack-hub-ruggedized"></a>将容器注册表添加到 Azure Stack 中心 (耐用) 

可以将容器注册表添加到 Azure Stack 中心 Marketplace，以便用户可以部署和维护其自己的容器注册表。 此解决方案模板将在 AKS 基 Ubuntu 16.04-LTS 映像上运行的用户订阅中安装并配置开源 Docker 容器注册表。 该模板支持 (气流) 部署的连接和断开连接，并支持 Azure Active Directory (AAD) 和 Active Directory (AD FS) 中心部署 Azure Stack 联合服务。

## <a name="get-the-marketplace-item"></a>获取 Marketplace 项

可在以下 GitHub 存储库中找到 **容器注册表模板** Marketplace 项： https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/registry-v1.0.2/Microsoft.AzureStackContainerRegistry.1.0.2.azpkg 。 Marketplace 项在门户的 "选择订阅" 中提供。

还可以使用 AzureStackDockerContainerRegistry 将项 (端负载) 添加到 Marketplace。 可以通过以下方式访问本文中的脚本：将 git 存储库下载为 (zip) 包 https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip ，并提取文件。 你可以在文件夹中找到该脚本 `azurestack-gallery-master\registry\scripts` 。

## <a name="prerequisites"></a>必备条件

在 Azure Stack Hub 上添加容器注册表 Marketplace 项之前，你将需要具有以下各项。

| 项目 | 类型 | 详细信息 |
| --- | --- | --- |
| Azure Stack 中心 PowerShell 模块 (Azs)  | PowerShell 模块 | 仅当边加载容器注册表模板库项时才需要，Azure Stack 中心 PowerShell 模块用于添加和删除库项。<br>[安装 Azure Stack PowerShell 模块](../../operator/azure-stack-powershell-install.md) |
| 容器注册表模板 | 市场项 | 若要以 Azure Stack 中心用户的身份部署容器注册表，则必须在订阅中提供容器注册表模板 Marketplace 项，或手动将 (端加载) 添加到 Azure Stack 中心市场。 如果边载，请按照中的说明将包加载到 `readme.md` [GitHub 存储库](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/registry-v1.0.1)中的。 |
| AKS 基本 Ubuntu 16.04-LTS 映像，9月2019最低发行版本 | 市场项 | 要使 Azure Stack 中心用户能够部署容器注册表，你必须将 AKS 基本映像提供给 Marketplace。 当承载 Docker 容器注册表二进制文件的 Ubuntu VM 时，容器注册表模板使用映像。 |
| Linux 自定义脚本扩展2。0 | 市场项 | 要使 Azure Stack 中心用户能够部署容器注册表，你必须使 Linux 自定义脚本扩展在 Marketplace 中可用。 容器注册表模板部署使用该扩展来配置注册表。 |
| SSL 证书 | 证书 | 部署容器注册表模板的用户需要提供在为注册表服务配置 SSL 加密时使用的 PFX 证书。 如果使用脚本，则需要从提升的提示符运行 PowerShell 会话。 不应在 DVM 或 HLH 上运行此。<br>有关使用公共或专用/企业证书 Azure Stack 中心的 PKI 证书要求的一般指导，请查看此文档，请参阅 [Azure Stack 中心公钥基础结构 (PKI) 证书要求](../../operator/azure-stack-pki-certs.md)<br>证书的 FQDN 应遵循此模式， `<vmname>.<location>.cloudapp.<fqdn>` 除非使用终结点的自定义域/dns 条目。 名称应以字母开头并包含至少两个字母，只使用小写字母，长度至少为三个字符。 |
| 服务主体 (SPN)  | 应用注册 | 若要部署和配置容器注册表，必须创建应用程序注册（也称为服务主体 (SPN) ）。 此 SPN 用于在 VM 和注册表的配置过程中访问部署 Marketplace 项之前创建 Microsoft Azure Key Vault 和存储帐户资源。<br>SPN 应在要登录到 Azure Stack 中心的用户门户的租户中的 AAD 中创建。 如果使用 AD FS，则将在本地目录中创建它。<br>有关如何为 AAD 和 AD FS 身份验证方法创建 SPN 的详细信息，请参阅 [以下指南](../../operator/azure-stack-create-service-principals.md)。<br> **重要提示**：需要保存 SPN 应用 ID 和机密，才能部署任何更新。<br> |
| 注册表用户名和密码 | 凭据 | 开源 docker 容器注册表已部署并配置为启用基本身份验证。 若要使用 docker 命令来推送和拉取映像，需要使用用户名和密码。 用户名和密码安全地存储在 Key Vault 存储区中。<br>**重要提示**：需要保存注册表用户名和密码才能登录到注册表和推送/提取映像。 |
| SSH 公钥/私钥 | 凭据 | 若要解决 VM 的部署或运行时问题的问题，需要为部署提供 SSH 公钥，并可访问相应的私钥。 建议使用 openssh 格式 ssh-keygen 生成私钥/公钥对，作为收集日志的诊断脚本需要此格式。<br>**重要提示**：需要访问公钥和私钥才能访问已部署的 VM 进行故障排除。 |
| 对管理员和用户门户和管理终结点的访问权限 | 连接性 | 本指南假设你要从连接到 Azure Stack 集线器系统的系统部署和配置注册表。 |

此脚本将 `Pre-reqs` 创建部署 Marketplace 项所需的其他输入。

## <a name="installation-steps"></a>安装步骤

在部署之前，安装容器注册表模板需要创建多个资源。

1. 使用 PowerShell 以用户身份连接到 Azure Stack 集线器，并使用 cmdlet 选择订阅 `Select-AzureRmSubscription –Subscription <subscription guid>` 。 有关以用户身份连接到 Azure Stack 集线器 PowerShell 的详细信息，请参阅 [使用 PowerShell 作为用户连接到 Azure Stack](../../user/azure-stack-powershell-configure-user.md)。

2. 运行 `Import-Modules .\\pre-reqs.ps1` 以导入脚本中的模块 `pre-reqs.ps1` 。 此脚本将创建资源组、存储帐户、blob 容器、Key Vault 存储、向 SPN 分配访问权限，并将注册表的证书和用户名和密码复制到 Key Vault 存储。

3. 使用参数的环境值，从提升的提示符下运行以下 cmdlet：

    ```powershell  
         Set-ContainerRegistryPrerequisites -Location Shanghai `
         -ServicePrincipalId <spn app id> `
         -ResourceGroupName newregreq1 `
         -StorageAccountName newregsa1 `
         -StorageAccountBlobContainer newregct1 `
         -KeyVaultName newregkv1 `
         -CertificateSecretName containersecret2 `
         -CertificateFilePath C:\crinstall\shanghairegcert.pfx `
         -CertificatePassword <cert password> `
         -RegistryUserName admin `
         -RegistryUserPassword <password> 
    ```

    | 参数 | 详细信息 |
    | --- | --- |
    | $Location | 这有时称为区域名称。 |
    | $ResourceGroupName | 指定要创建的存储帐户和 Key Vault 存储的资源组的名称。 部署 Marketplace 项时，将指定不同的资源组。 |
    | $StorageAccountName | 指定要为容器注册表创建的存储帐户的名称，以便在存储已推送的映像时使用。 |
    | $StorageAccountBlobContainer | 指定要创建用于映像存储的 blob 容器的名称。 |
    | $KeyVaultName | 指定要创建的 Key Vault 存储的名称以存储证书和用户名和密码值。 |
    | $CertificateSecretName | 提供在 Key Vault 中创建的用于存储 PFX 证书的机密的名称。 |
    | $CertificateFilePath | 提供 PFX 证书的路径。 |
    | $CertificatePassword | 提供 PFX 证书的密码。 |
    | $ServicePrincipalId | 提供 SPN 的 AppID。 |
    | $RegistryUserName | 提供使用基本授权访问注册表服务的用户名。 |
    | $RegistryUserPassword | 为注册表用户提供密码。 |

1. 脚本完成后，请注意脚本的末尾包含要在模板部署中使用的参数。 复制和粘贴这些值时，如果值换行，则可能会出现空格。

    ```powershell  
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/<subcription id>/resourceGroups/newr
    egreg1/providers/Microsoft.KeyVault/vaults/newregkv1
    PFX KeyVaultSecretUrl        : https://newregkv1.vault.shanghai.azurestack.corp.microsoft.com:443/secr
    ets/containersecret1/37cc2f7ea1c44ad7b930e2c237a14949
    PFX Certificate Thumbprint   : 64BD5F3BC41DCBC6495998900ED322D8110DE25E
    ----------------------------------------------------------------
    StorageAccountResourceId     : /subscriptions/<subcription id>/resourcegroups/newr
    egreg1/providers/Microsoft.Storage/storageAccounts/newregsa1
    Blob Container               : newregct1
    ----------------------------------------------------------------
    
    Skus : aks-ubuntu-1604-201909
    
    
    Skus : aks-ubuntu-1604-201910
    
    ---------------------------------------------------------------- 
    
    ```

1. 打开 Azure Stack Hub 用户门户。

2. 选择 "**创建**  >  **计算**  >  **容器注册表模板**"。

    ![屏幕截图，显示 "仪表板 > 新" 页面，其中选择了 "计算" 并显示 "容器注册表模板" 选择。](./media/container-registry-template-install-tzl/image1.png)

3. 选择订阅、资源组和部署容器注册表模板的位置。

    ![屏幕截图，显示 "创建容器注册表模板-基础知识" 页。](./media/container-registry-template-install-tzl/image2.png)

4. 完成虚拟机配置详细信息。 图像 SKU 默认为 **aks-1604-201909**;但是，该函数的输出 `Set-ContainerRegistryPrerequisites` 包含用于部署的可用 sku 的列表。 如果存在多个 SKU，请选择最新的用于部署的 SKU。

    ![显示 "创建容器注册表模板-虚拟机配置" 页面的屏幕截图。](./media/container-registry-template-install-tzl/image3.png)

    | 参数 | 详细信息 |
    | --- | --- |
    | 用户名 | 提供登录到 VM 的用户名。 |
    | SSH 公钥 | 提供使用 SSH 协议向 VM 进行身份验证的 SSH 公钥。 |
    | 大小 | 选择要部署的 VM 的大小。 |
    | 公共 IP 地址 | 为此 VM 指定 (动态或静态) 的 IP 地址名称和类型。 域名无效。 该名称只能包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 值的长度必须介于三到63个字符之间。  |
    | 域名标签 | 指定注册表的 DNS 前缀。 整个 FQDN 应与为注册表创建的 PFX 证书的 CN 值匹配。 |
    | 副本 | 指定要启动的容器副本数。 |
    | 映像 SKU | 指定要用于部署的映像 SKU。 此脚本列出了 AKS 基本映像的可用 Sku `Set-ContainerRegistryPrerequisites` 。 |
    | SPN 客户端 ID | 指定 SPN 应用 ID。 |
    | SPN 密码/确认密码 | 指定 SPN 应用 ID 机密。 |

1. 完成存储并 Key Vault 配置。

    ![显示 "创建容器注册表模板-存储和 Key Vault 配置" 页的屏幕截图。](./media/container-registry-template-install-tzl/image4.png)

    | 参数 | 详细信息 |
    | --- | --- |
    | 现有的扩展存储帐户资源 ID | 指定脚本返回的存储帐户资源 ID `pre-reqs` 。 |
    | 现有后端 blob 容器 | 指定请求脚本输出中列出的 blob 容器名称。 |
    | PFX 证书 Key Vault 资源 ID | 指定脚本返回的 Key Vault 资源 ID `pre-reqs` 。 |
    | PFX 证书 Key Vault 机密 URL | 指定请求脚本返回的证书 URL。 |
    | PFX 证书指纹 | 指定请求脚本返回的证书指纹。 |

1. 提供所有值并且解决方案模板的部署开始后，VM 需要10-15 分钟才能部署和配置注册表服务。

    ![容器注册表模板](./media/container-registry-template-install-tzl/image5.png)

2. 若要测试注册表，请从可访问注册表 URL 的计算机/VM 打开 docker CLI 实例。

    > [!Note]
    >  如果你使用的自签名证书或证书对用于访问注册表的 VM 未知，则需要在 VM 上安装该证书，并重新启动 Docker。

## <a name="pushing-and-pulling-images-from-container-registry"></a>从容器注册表推送和拉取映像

1. 使用登录 `docker login –u \<username> -p \<password>` 。
2. 从已知注册表请求和映像。
3. 将映像标记为面向新部署的 docker 容器注册表。
4. 将映像推送到新的目标注册表。

例如：

```powershell  
PS C:\> docker pull mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
1.0: Pulling from azureiotedge-simulated-temperature-sensor
5d20c808ce19: Already exists
656de8e592c3: Already exists
1e1868d1f676: Already exists
f3fb1b0d620f: Pulling fs layer
26224c4fc11a: Pulling fs layer
c459a69d65b2: Pulling fs layer
c459a69d65b2: Verifying Checksum
c459a69d65b2: Download complete
f3fb1b0d620f: Download complete
f3fb1b0d620f: Pull complete
26224c4fc11a: Verifying Checksum
26224c4fc11a: Pull complete
c459a69d65b2: Pull complete
Digest: sha256:dd64ff0918459184574e840ee97aa9f1bacd40aa37c972984ea10f0ecd719d5f
Status: Downloaded newer image for mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker tag mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0    myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp.microsoft.com
docker : WARNING! Using --password via the CLI is insecure. Use --password-stdin.
At line:1 char:1
+ docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (WARNING! Using ...password-stdin.:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
 
Login Succeeded

PS C:\> docker push myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
The push refers to repository [myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor]
d377c212e567: Preparing
0481685b758f: Preparing
15474c03a0b6: Preparing
8cdec5be5964: Preparing
79116d3fb0bf: Preparing
3fc64803ca2d: Preparing
3fc64803ca2d: Waiting
79116d3fb0bf: Mounted from azureiotedge-agent
8cdec5be5964: Mounted from azureiotedge-agent
15474c03a0b6: Pushed
d377c212e567: Pushed
3fc64803ca2d: Mounted from azureiotedge-agent
0481685b758f: Pushed
1.0: digest: sha256:f5fbc4a5c6806e12cafe1c363fea2b6cbd98a211b8153c5b19aca1386bfa6ecb size: 1576 

```

## <a name="known-issues"></a>已知问题

此模板部署的 Docker 容器注册表服务的版本为2.7。 此版本有一个已知问题，可阻止推送和拉取 Windows 容器映像。 此问题通过以下 GitHub 项进行跟踪 [https://github.com/docker/distribution-library-image/issues/89](https://github.com/docker/distribution-library-image/issues/89) 。

## <a name="next-steps"></a>后续步骤

[Azure Stack 市场概述](../../operator/azure-stack-marketplace.md)
