---
title: 在 Azure Stack 集线器上部署图形处理单元 (GPU) 启用的 IoT 模块
description: 如何在 Azure Stack Hub 上将图形处理单元部署 (GPU) 启用的 IoT 模块
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/13/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: b8aef224c5694cd6b8408538473dce3b86996c4d
ms.sourcegitcommit: 8187658b1d45dceed727aca3ae1b9b57aca04392
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2020
ms.locfileid: "94632783"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>在 Azure Stack 集线器上部署支持 GPU 的 IoT 模块

使用启用了 GPU 的 Azure Stack 集线器，你可以将处理器密集型模块部署到在 IoT Edge 运行的 Linux 设备。 GPU 优化 VM 大小是适用于单个或多个 NVIDIA Gpu 的专用 Vm。 本文介绍如何使用 GPU 优化 Vm 运行计算密集型、图形密集型和可视化工作负荷。

在开始之前，你将需要一个 Azure Active Directory (Azure AD) 订阅，可以访问全局 Azure 和 Azure Stack 中心、Azure 容器注册表 (ACR) 和 IoT 中心。

本文内容：
  - 安装支持 GPU 的 Linux VM 并安装正确的驱动程序。
  - 安装 Docker 并在运行时中启用 GPU。
  - 将 IoT 设备连接到 iOT 中心并从 iOT marketplace 安装模型： `Getting started with GPUs` 。
  - 使用 Azure IoT 浏览器从本地计算机安装和监视设备。
  - 另外，还可以使用 Visual Studio Code 中的 Azure IoT 扩展安装和监视设备。

## <a name="prerequisites"></a>先决条件

你需要在 Azure Stack 中心实例、全球 Azure 和本地开发计算机上部署以下资源。

### <a name="azure-stack-hub-and-azure"></a>Azure Stack 中心和 Azure

  - 使用 Azure Active Directory (用户的订阅在 Azure Stack 中心集成系统中使用 NVIDA GPU Azure AD) 。 以下芯片适用于 iOT 中心：
    - NCv3
    - NCas_v4

    有关 Azure Stack 集线器上 Gpu 的详细信息，请参阅 [Azure Stack 集线器上的图形处理单元 (GPU) VM](gpu-vms-about.md)。
  - 全局 Azure 订阅。 如果你没有全球 Azure 订阅，请在开始之前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
- [Azure 容器注册表 (ACR) ](/)。 记下 ACR 登录服务器、用户名和密码。
-   全局 Azure 中的免费或标准层 [IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) 。

### <a name="a-development-machine"></a>开发计算机

-   可以使用自己的计算机或虚拟机，具体取决于开发首选项。 开发计算机将需要支持嵌套虚拟化。 此功能对于运行 Docker （本文中使用的容器引擎）是必需的。

  - 开发计算机将需要以下资源：
      - [Python 3.x](https://www.python.org/downloads/)
      - 用于安装 Python 程序包的 [Pip](https://pypi.org/project/pip/)。 这是随 Python 安装一起安装的。 如果已安装 Pip，则可能需要升级到最新版本。 您可以使用 pip 来升级自身。 键入：`pip install --upgrade pip`。
      - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)
      - [Git](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
      - 适用于 Visual Studio Code 的[Python 扩展包](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack)

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用单独的设备来托管 IoT Edge 设备。 使用单独的设备将在你的开发计算机与 IoT Edge 设备之间提供分离，以便更准确地反映部署方案。 

使用 Linux VM 在 Azure 中创建 IoT Edge 设备：

1.  在 Azure Stack 集线器上[创建 N 系列 Linux 服务器 VM](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal) 。 为服务器安装组件时，会通过 SSH 与服务器进行交互。 有关详细信息，请参阅[使用 SSH 公钥](/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key)。

2.  [创建并注册 IoT Edge 设备](https://docs.microsoft.com/azure/iot-edge/how-to-register-device)

## <a name="prepare-a-gpu-enabled-vm"></a>准备启用了 GPU 的 VM

1. 按照在 [运行 Linux 的 n 系列 vm 上安装 NVIDIA GPU 驱动程序](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)一文中的步骤操作，在 n 系列 Linux 服务器上安装 NVIDA GPU 驱动程序。

    > [!NOTE]  
    > 你将使用 bash 命令行来安装软件。 请记下这些命令，因为你将使用相同的命令将驱动程序安装在启用了 GPU 的 VM 上的 Docker 中运行的容器上

2.  在 Azure Stack 集线器中的 N 系列 Linux 服务器上安装最新的 IoT Edge 运行时。 有关说明，请参阅 [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](/azure/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version)

## <a name="install-docker"></a>安装 Docker

在启用 GPU 的 VM 上安装 Docker。 你将从 VM 上的容器中的 IoT Edge marketplace 运行该模块。

必须安装 Docker 19.02 或更高版本。 Docker 运行时现在支持 NVIDIA Gpu。 若要在 Docker 中了解更多 bout Gpu，请参阅 Docker 文档中的文章： [内存、cpu 和 gpu 的运行时选项](https://docs.docker.com/config/containers/resource_constraints/#gpu)。

### <a name="install-docker"></a>安装 Docker

Docker 容器可以在任意位置运行：在客户数据中心本地、在外部服务提供商或在 Azure 云中。 Docker 映像容器可以在 Linux 和 Windows 上本机运行。 但是，Windows 映像仅能在 Windows 主机上运行，Linux 映像可以在 Linux 主机和 Windows 主机上运行（到目前为止，使用 Hyper-V Linux VM），其中主机是指服务器或 VM。 有关详细信息，请参阅 [什么是 Docker？](https://docs.microsoft.com/dotnet/architecture/microservices/container-docker-introduction/docker-defined)。

1. 使用 SSH 客户端连接到 N 系列 Linux 服务器。

1.  更新 apt 索引和列表。

    ```bash  
    sudo apt-get update
    ```

1.  提取计算机上现有包的新版本。

    ```bash  
    sudo apt-get upgrade
    ```

2.  安装添加 Docker 的 apt 存储库所需的依赖项。

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  添加 Docker 的 GPG 密钥。

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  添加 Docker 的 apt 存储库。

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  更新 apt 索引和列表，并安装 Docker 社区版。

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  通过检查 Docker 版本来验证安装。

    ```bash  
    docker -v
    ```

7. 在 Docker 中公开可用 GPU 资源。

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>从 marketplace 获取项

返回到 Azure 门户，并使用 iOT marketplace 将模型添加到 edge 设备。 从菜单中选择 " **Marketplace 模块** "。 并 `Getting started with GPUs` 按说明进行操作，以添加模块。

有关说明，请参阅 [选择设备和添加模块](/azure/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules)

## <a name="enable-monitoring"></a>启用监视

1. 下载 [Azure IoT 资源管理器](/azure/iot-pnp/howto-use-iot-explorer)，并将应用程序连接到 IoT 中心。

2. 选择 IoT 设备，并从导航菜单导航到 "遥测"。

3. 选择 " **开始** " 以开始监视 IoT Edge 设备的输出。

![有效安装](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>监视模块 (可选)   

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 在这种情况下，请选择用于部署 Azure Stack Edge 设备的订阅，并选择为 Azure Stack 边缘设备创建的 IoT Edge 设备。 在前面的步骤中通过 Azure 门户配置计算时，会发生这种情况。

3. 在 VS Code 资源管理器中，展开 "Azure IoT 中心" 部分。 在 " **设备** " 下，应会看到与 Azure Stack 边缘设备对应的 IoT Edge 设备。 

    1. 选择该设备，右键单击，然后选择 " **开始监视内置事件终结点** "。

    2. 中转到 " **设备" > 模块** ，你会看到你的 **GPU 模块** 正在运行。

    3. VS Code 终端还应显示 IoT 中心事件作为 Azure Stack Edge 设备的监视输出。

    ![有效的安装是](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    你可以看到，执行同一组操作所需的时间 (5000 的形状转换) 迭代的时间比 CPU 的小。

## <a name="next-steps"></a>后续步骤

  - [在 Azure Stack 中心上详细了解 (GPU) 虚拟机 (VM) 的图形处理单元](gpu-vms-about.md)

  - 了解有关 Azure Stack 中心、Data Box Edge 和智能边缘的详细信息，请 [参阅未来的计算：智能云和智能边缘](https://azure.microsoft.com/overview/future-of-cloud)

  - 了解有关混合云应用程序的详细信息，请参阅 [混合云解决方案](https://docs.microsoft.com/hybrid/app-solutions/)