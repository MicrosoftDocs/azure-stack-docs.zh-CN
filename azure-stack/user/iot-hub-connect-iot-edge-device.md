---
title: 将 IoT Edge 设备连接到 Azure Stack 集线器上的 IoT 中心
description: 了解如何将 IoT Edge 设备连接到 Azure Stack 集线器上的 IoT 中心。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/20/2020
ms.reviewer: dmolokanov
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 768952db6d93930f710dd80ea10e23ae6d8297a5
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920622"
---
# <a name="connect-an-iot-edge-device-to-iot-hub-on-azure-stack-hub"></a>将 IoT Edge 设备连接到 Azure Stack 集线器上的 IoT 中心

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

本文介绍如何将虚拟 IoT Edge 设备连接到 Azure Stack 集线器上运行的 IoT 中心。 可以使用相同的常规过程将物理设备连接到 IoT 中心。

## <a name="prerequisites"></a>必备条件

在继续之前，请先完成以下先决条件：

- 你将需要一个帐户，该帐户至少具有["参与者" 权限](../user/azure-stack-manage-permissions.md)才能访问[Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md)。

- 请与管理员合作， [在 Azure Stack Hub 资源提供程序上安装 IoT 中心](../operator/iot-hub-rp-overview.md)。 安装步骤还介绍了如何创建包含 IoT 中心服务的产品/服务。 

- 提供产品/服务后，管理员可以创建或更新 Azure Stack 中心订阅以包含 IoT 中心。 或者，你可以[订阅新产品/服务并创建自己的订阅](azure-stack-subscribe-services.md)。

- 基本了解 (PKI) 的公钥加密会很有帮助。 具体来说，证书颁发机构 (CA) 和 X509 证书的方式用于构建信任链。 这种信任允许网络节点安全地进行身份验证和通信，如 IoT 中心服务和 IoT Edge 设备。 

## <a name="overview"></a>概述

下面概述了将 IoT Edge 设备连接到 Azure Stack 集线器上的 IoT 中心所要完成的步骤：

1. 在 Azure Stack 中心实例上创建 IoT 中心和 Linux VM 资源。 Linux VM 将用作虚拟 IoT Edge 设备。 
2. 配置 IoT Edge 设备所需的证书。 所需的步骤数取决于颁发 IoT 中心根 CA 证书的 CA 类型。
3. 配置 IoT Edge 设备所需的软件和服务。
4. 测试 IoT Edge 设备，确保其正常工作，并与 IoT 中心进行通信。

在每个部分中运行该脚本之前，请确保更新脚本占位符，使其与环境的配置相匹配。 记下你使用的值，因为你将在后面的部分中需要它们：

| 占位符 | 示例 | 描述 |
|-------------|---------|-------------|
| `<DEVICE-CA-CERT-NAME>` | `edged1cert`| 为 IoT Edge 设备 CA 证书指定的名称。 |
| `<IOT-HUB-HOSTNAME>`| `test-hub-1.mgmtiothub.region.mydomain.com`| IoT 中心主机名。 |
| `<IOT-HUB-ROOT-CA>`| `root.cer`| 如果使用私有 CA) ，则为导出的 IoT 中心根 CA 指定的文件名 (。 |
| `<DATA-DIR>`| `/home/edgeadmin/edged1`| 在 Linux VM 上创建的数据目录的完整路径。 |
| `<USER-ACCOUNT>`| `edgeadmin`| 用于登录到 Linux VM 的帐户。 |

## <a name="create-azure-stack-hub-resources"></a>创建 Azure Stack 集线器资源

首先创建必要的 Azure Stack 集线器资源，包括 IoT 中心和 Linux VM。 你将使用 [Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md) 来创建这些资源。

### <a name="create-an-iot-hub-resource"></a>创建 IoT 中心资源

1. 从有权访问 Azure Stack 中心实例的计算机登录到 [Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md) 。
2. 如果尚未创建一个 iot 中心，请按照 **使用 Azure 门户创建 iot**[中心部分中的说明](/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)创建 iot 中心资源。 

   > [!IMPORTANT]
   > 请确保按照本文中的步骤操作，而不是在 Azure 门户中使用 [Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md) 。 另请注意，某些屏幕截图和说明可能略有不同，因为并不是所有的 Azure 功能都在 Azure Stack 集线器上提供。 

### <a name="deploy-and-configure-a-linux-vm"></a>部署和配置 Linux VM 

在本部分中，将部署新的 Linux VM，该 VM 将用作虚拟 IoT Edge 设备：

1. 通过使用 [Azure Stack 中心门户快速入门创建 linux 服务器 vm](azure-stack-quick-linux-portal.md) ，在 Azure Stack 中心实例上安装 linux VM。 转到 "**设置**" 页面时，请确保在 "**选择公用入站端口**" 属性上启用端口 **SSH (22)** 。 

   > [!NOTE]
   > 你只需完成前5个部分（通过 **连接到 VM**），因为你不需要 NGINX web 服务器。 

2. 使用 PuTTY 创建并连接到 VM 后，请在用户帐户的主目录中创建数据子目录，例如：

   ```bash
   mkdir edged1
   ```

3. 使用以下方法之一设置证书生成工具。 将从 IoT Edge GitHub 存储库下载文件，之后需要生成设备证书：

   **使用卷曲脚本：**
   ```bash
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Download certGen.sh script file, and openssl_root_ca.cnf config file
   curl -Lo certGen.sh https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/certGen.sh
   curl -Lo openssl_root_ca.cnf https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/openssl_root_ca.cnf
   ```
   **使用 Git 克隆存储库：**
   
   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>
   
   # Clone the iotedge repo, which contains the certGen.sh script file and openssl_root_ca.cnf config file
   git clone https://github.com/Azure/iotedge.git
   
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Copy certGen.sh and openssl_root_ca.cnf into your working directory
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/*.cnf .
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/certGen.sh .
   ```

## <a name="configure-iot-edge-device-certificates"></a>配置 IoT Edge 设备证书

在本部分中，你将完成虚拟 IoT Edge 设备所需的 VM 证书配置。 

IoT 中心服务和 IoT Edge 设备通过 X509 证书进行保护。 两者都必须使用同一个 CA 颁发的根 CA 证书。 根据 IoT 中心所用的根 CA 类型，选择下面相应的选项卡来完成证书配置。

# <a name="public-ca"></a>[公共 CA](#tab/public-ca)

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

# <a name="private-ca"></a>[私有 CA](#tab/private-ca)

> [!IMPORTANT]
> 如果使用的是私有 CA，则需要执行其他步骤才能将 IoT 中心根 CA 证书传输到 IoT Edge 设备。 仅当使用自己的私有 CA 进行证书生成时，**才** 需要完成这些步骤。 如果你的 IoT 中心根 CA 证书是由公共 CA 颁发的，请选择前面的 _ *公共 ca** 选项卡。 

### <a name="export-the-root-ca-certificate-from-your-iot-hub"></a>从 IoT 中心导出根 CA 证书

使用可访问 Azure Stack 中心的计算机，以 PEM 格式导出 IoT 中心根 CA 证书。 下面的示例演示如何使用 [Microsoft Edge](https://www.microsoft.com/edge) 或 [Google Chrome](https://www.google.com/chrome/index.html) 浏览器导出证书： 

   1. 在 IoT 中心的 "**概述**" 页上，使用 "**主机名**" 属性右侧的 "**复制**" 按钮将 IoT 中心主机名复制到剪贴板：  

      [![IoT 中心主机名](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png)](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png#lightbox)
   1. 在 Microsoft Edge 或 Google Chrome 浏览器中打开新的选项卡，输入 `https://` ，粘贴在上一步中复制的 IoT 中心主机名，然后按 **enter**。 

   1. 返回错误消息后，选择地址栏左侧的锁定图标，然后选择 " **证书**"。
      [![证书安全连接](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png#lightbox)

   1. SSL/TLS 证书显示时，请选择 " **证书路径** " 选项卡。然后在路径中选择最顶层的证书，然后选择 " **查看证书** " 按钮。  
      [![SSL 证书的证书安全连接](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png#lightbox) 

   1. 当根 CA 证书显示时，选择 " **详细信息** " 选项卡，然后选择 " **复制到文件 ...** " 按钮。
      [![根 CA 证书的证书安全连接](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png#lightbox) 

   1. 显示 " **证书导出向导** " 时，将证书导出到 **64 编码的 x.509 格式** 文件，例如， **root**。 在下一部分中使用此文件，因此请将其导出到可从 Linux VM 访问或复制到 Linux VM 的位置。

   1. 导出成功完成后，可以关闭浏览器选项卡。

### <a name="install-the-iot-hub-root-ca-certificate-on-the-iot-edge-device"></a>在 IoT Edge 设备上安装 IoT 中心根 CA 证书

现在，将上一节中导出的 IoT 中心根 CA 证书安装到 IoT Edge 设备的受信任存储区中。 

1. 将上一节中导出的 IoT 中心根 CA 文件传输到 IoT Edge 设备。 由于我们使用 Linux VM 作为 IoT Edge 设备，因此需要将其复制到 Linux VM。 根据你的环境，请考虑使用 PuTTY 和 PSCP 命令，或使用 WinSCP 程序将文件复制到 Linux VM。
2. 从你已存储 IoT 中心根 CA 文件的 Linux VM PuTTY 会话运行以下脚本。 此脚本将验证 TLS 连接是否成功，并在 IoT Edge 设备的受信任存储区中安装根 CA：

   ```bash
   # Verify connection failed first
   # Expected response: Verify return code: 2 (unable to get issuer certificate)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   
   # Verify connection succeeded when root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443 -CAfile <IOT-HUB-ROOT-CA>
   
   # Install root CA in the trusted store on Edge device
   sudo cp <IOT-HUB-ROOT-CA> /usr/local/share/ca-certificates/
   sudo update-ca-certificates
   
   # Verify connection succeeded even when no root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   ```

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

### <a name="append-the-iot-hub-root-ca-to-the-device-root-ca"></a>将 IoT 中心根 CA 附加到设备根 CA

现在，将导出并复制到 IoT Edge 设备的 IoT 中心根 CA 附加到之前生成的设备根 CA：

```bash
# Navigate to home directory
cd /home/<USER-ACCOUNT>

# Append IoT Hub root CA to the device root CA
cat <IOT-HUB-ROOT-CA> >> certs/azure-iot-test-only.root.ca.cert.pem
```
-----

## <a name="configure-iot-edge-device-software-and-services"></a>配置 IoT Edge 设备软件和服务

在本部分中，你将完成虚拟 IoT Edge 设备所需的 IoT 中心和 VM 配置。

### <a name="install-iot-edge-and-container-runtimes-on-the-vm"></a>在虚拟机上安装 IoT Edge 和容器运行时

1. 返回到 VM PuTTY 会话并运行以下脚本，以注册 Microsoft 密钥和软件存储库源：

   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>

   # Install the repository configuration. 
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   
   # Copy the generated list.
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   
   # Install Microsoft GPG public key.
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

2. 安装容器运行时：

   ```bash
   sudo apt-get update
   sudo apt-get install moby-engine moby-cli
   ```

3. 安装 Azure IoT Edge 安全守护程序：

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

> [!NOTE]
> 你可以忽略 **重要：请立即更新配置文件** 通知，因为稍后将对其进行更新。

### <a name="create-an-iot-edge-device-in-iot-hub"></a>在 IoT 中心创建 IoT Edge 设备

1. 返回 [Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md)，并导航到 **IoT 中心** 服务。

2. 选择 IoT 中心资源，然后选择 " **IoT Edge** " 页，然后 **添加 IoT Edge 设备**。

   [![IoT 中心资源](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png#lightbox)

3. 在 " **创建设备** " 页上，输入 **设备 ID**，例如 "edged1"。
4. 完成后，选择“保存”。

   [![IoT Edge-创建设备](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png#lightbox)

5. 当门户返回到 " **IoT Edge** " 页面时，请从 "设备" 列表中选择你的新设备。

   [![IoT Edge-查看设备](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png)](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png#lightbox)

6. 在 "设备详细信息" 页上，使用 "**主连接字符串**" 右侧的 "**复制**" 按钮将字符串复制到剪贴板。 下一节将使用连接字符串。

   [![IoT Edge 设备详细信息](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png#lightbox)

### <a name="configure-the-virtual-iot-edge-device-on-the-vm"></a>在虚拟机上配置虚拟 IoT Edge 设备

1. 返回到 VM PuTTY 会话。 使用 Bash 在虚拟 IoT Edge 设备上打开 Nano 中的配置文件：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

2. `provisioning`使用连接字符串在 "**预配模式" 和 "设置**" 部分中找到 "注释 **# 手动设置配置**" 下的属性。 通过将 `<ADD DEVICE CONNECTION STRING HERE>` 占位符替换为在上一部分复制到剪贴板的连接字符串来更新 IoT Edge 设备连接字符串：

   > [!NOTE]
   > 若要将剪贴板内容粘贴到 Nano，请按住 **Shift** 键并单击鼠标右键。 或者，同时按 **Shift** 和 **Insert** 键。 如果粘贴操作将光标移到连接字符串的最右端，则按 **Home** 键返回到最左端。

   ```yaml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

3. `certificates`在 "**证书设置**" 部分中找到并取消注释属性。 取消注释并将文件 Uri 替换为之前创建的三个证书的路径，例如：

   ```yaml
   certificates:
     device_ca_cert: "<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem"
     device_ca_pk: "<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem"
     trusted_ca_certs: "<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

4. 完成后， `provisioning` 和 `certificates` 属性应类似于以下响应：

   [![Nano 编辑器-预配属性](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png#lightbox)

   [![Nano 编辑器-证书属性](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png#lightbox)


5. 使用 **CTRL** X 和 Y 保存并关闭该文件，  +  然后按 **Enter** 。

6. 返回到 Bash 后，重新启动守护程序：
   
   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-a-successful-installation"></a>验证安装是否成功

1. 检查 IoT Edge 守护程序的状态：

   ```bash
   systemctl status iotedge
   ```

2. 应该会看到 IoT Edge 服务正在运行，并且有一个如下所示的响应。 如果是这样，则可以跳转到步骤 #4。

   [![IoT Edge 服务成功运行](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png#lightbox)

3. 如果 IoT Edge 服务失败：
   - 你将看到类似于 "失败" 的响应，类似于以下示例： [ ![ IoT Edge service 失败](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png#lightbox)   
   - 若要解决此问题，可以：
     - 检查守护程序日志：

         ```bash
         journalctl -u iotedge --no-pager --no-full
         ```
     - 运行故障排除工具，检查最常见的配置和网络错误。 在此示例中，检测到一个格式不正确的 `/etc/iotedge/config.yaml` 文件：

         ```bash
         sudo iotedge check
         ```

         [![IoT Edge 服务检查](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png#lightbox)

         > [!NOTE]
         > " `$edgeHub` 系统模块" 是可选的，除非部署第一个 IoT Edge 模块，否则不会将其部署到设备。 因此， `iotedge check` 可能会返回一个错误，指示 `$edgeHub` 在主机连接检查期间无法绑定到端口。 如果部署中不需要此错误，则可以将其忽略 `$edgeHub` 。           

   - 如果发现格式不正确。如上例所示，DOCKER-COMPOSE.OVERRIDE.YML 文件如下所示：
      - 重复在 [VM 上配置虚拟 IoT Edge 设备](#configure-the-virtual-iot-edge-device-on-the-vm) 中的步骤来修复。DOCKER-COMPOSE.OVERRIDE.YML 文件。
      - 重复 [验证安装](#verify-a-successful-installation)中的步骤。

4. 确定 IoT Edge 服务成功启动后，列出正在运行的模块。 你应看到服务的 `edgeAgent` 状态为 `running` ：

   ```bash
   sudo iotedge list
   ```

## <a name="clean-up-resources"></a>清理资源

如果不再使用这些资源，请返回 [Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md) ，并删除之前创建的 VM 和 IoT 中心资源。

## <a name="next-steps"></a>后续步骤

下面是补充资源：

- [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](/azure/iot-edge/how-to-install-iot-edge-linux)
- [配置 IoT Edge 设备以充当透明网关](/azure/iot-edge/how-to-create-transparent-gateway)
- [创建演示证书用于测试 IoT Edge 设备功能](/azure/iot-edge/how-to-create-test-certificates)