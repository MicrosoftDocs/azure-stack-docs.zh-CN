---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 11/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 6d4bb587ad28fb26adf60689989d0e1858519cfe
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920620"
---
### <a name="create-a-device-root-ca-certificate"></a>创建设备根 CA 证书

为设备创建所需的设备根 CA 证书和密钥文件： 

1. 返回到 PuTTY 会话中的 Bash 命令提示符。
2. 导航到在上一部分中放置证书生成脚本的数据目录。
3. 运行以下命令：

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

4. 设备根 CA 证书存储在以下文件中： `<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem` 。

### <a name="create-the-iot-edge-device-ca-certificate"></a>创建 IoT Edge 设备 CA 证书

生产 IoT Edge 设备需要从 yaml 文件引用的设备 CA 证书。 设备 CA 证书负责为设备上运行的模块创建证书。 对于网关方案也是必需的，因为 IoT Edge 设备使用设备 CA 证书来验证其身份是否为下游设备。

若要创建 IoT Edge 设备 CA 证书文件：

1. 返回到 PuTTY 会话中的 Bash 命令提示符。
2. 运行以下脚本，该脚本会创建多个设备 CA 证书和密钥文件： 

   ```bash
   # Navigate to data directory
   cd <DATA-DIR>
   
   # Generate IoT Edge device CA certificate 
   ./certGen.sh create_edge_device_ca_certificate <DEVICE-CA-CERT-NAME>
   ```

3.  稍后将在 yaml 文件中引用以下证书和密钥对文件：

    `<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem`  
    `<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem`


