---
title: 使用社区模板创建 VM |Microsoft Docs
description: 了解如何使用 Azure Stack 开发工具包 (ASDK) 使用预定义的模板和 GitHub 自定义模板创建 VM。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: d82cdb1cccd52cc5e4c11571d85914be3f79a74e
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691576"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>教程：使用社区模板创建 VM

作为 Azure Stack 操作员或用户，你可以创建虚拟机 (VM) 使用[自定义 GitHub 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)而不是部署的模板手动从 Azure Stack marketplace。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Azure Stack 快速入门模板
> * 使用自定义 GitHub 模板创建 VM
> * 启动 Minikube 并安装应用程序

## <a name="azure-stack-quickstart-templates"></a>Azure Stack 快速入门模板

Azure Stack 快速入门模板存储在 GitHub 中[全局 Azure Stack 快速入门模板存储库](https://github.com/Azure/AzureStack-QuickStart-Templates)。 此存储库 （存储库） 包含已经过测试与 Microsoft Azure Stack 开发工具包 (ASDK) 的 Azure 资源管理器部署模板。 可以通过这些模板更轻松地评估 Azure Stack 并使用 ASDK 环境。

随着时间推移，许多 GitHub 用户做出过贡献存储库，从而导致 400 多个部署模板的集合。 此存储库是更好地了解如何将各种类型的环境部署到 Azure Stack 的良好起点。

>[!IMPORTANT]
> 这些模板中的部分模板是由社区成员创建的，不是由 Microsoft 创建的。 每个模板按照其所有者而非 Microsoft 的许可协议进行许可。 Microsoft 不对这些模板负责，也不检查其安全性、符合性或性能方面的问题。 社区模板不受任何 Microsoft 支持计划或服务，并提供"按原样，"无任何形式的保证。

如果你想要参与到 GitHub 的 Azure 资源管理器模板，请将其贡献给[AzureStack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)存储库。 若要了解有关此存储库以及如何向其贡献内容的详细信息，请参阅[自述文件](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)。

## <a name="create-a-vm-using-a-custom-github-template"></a>使用自定义 GitHub 模板创建 VM

本示例教程[101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure Stack 快速入门模板用于部署到运行 Minikube 来管理 Kubernetes 群集的 Azure Stack 上的 Ubuntu 16.04 VM。

Minikube 是一种工具，方便用户在本地运行 Kubernetes。 Minikube 在 VM 中运行单节点 Kubernetes 群集，使你可以试用 Kubernetes 或使用它进行日常开发。 它支持在 Linux VM 上运行的简单的单节点 Kubernetes 群集。 Minikube 是运行功能齐全的 Kubernetes 群集的最快、最直接的方法。 开发人员可以使用它在本地计算机上开发和测试基于 Kubernetes 的应用程序部署。 体系结构方面，Minikube VM 主机和代理节点组件在本地运行：

* 主节点组件，例如 API 服务器，计划程序，并[etcd 服务器](https://coreos.com/etcd/)调用的单个 Linux 进程中运行**为 LocalKube**。
* 代理节点组件是在 docker 容器中运行，它们会在正常代理节点上运行的样子。 从应用程序部署角度来看，部署在 Minikube 上或在常规 Kubernetes 群集中的应用程序之间没有区别。

此模板安装以下组件：

* Ubuntu 16.04 LTS VM
* [Docker-CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> Ubuntu VM 映像 (Ubuntu Server 16.04 LTS，在此示例中) 必须具有已添加到 Azure Stack marketplace 之前执行这些步骤。

1. 依次选择“+ 创建资源”、“自定义”、“模板部署”。   

    ![创建模板](media/azure-stack-create-vm-template/1.PNG)

2. 选择“编辑模板”。 

    ![编辑模板](media/azure-stack-create-vm-template/2.PNG)

3. 选择“快速入门模板”。 

    ![快速入门模板](media/azure-stack-create-vm-template/3.PNG)

4. 使用“选择模板”下拉列表从可用模板中选择 **101-vm-linux-minikube**，  然后单击“确定”。 

    ![选择模板](media/azure-stack-create-vm-template/4.PNG)

5. 如果你想要对 JSON 的模板进行修改，就可以做到。 如果不想要进行修改或者已完成操作，请选择“保存”关闭“编辑模板”对话框。  

    ![保存模板](media/azure-stack-create-vm-template/5.PNG)

6. 选择“参数”，根据需要填充或修改适用的字段，然后单击“确定”。  

    ![parameters](media/azure-stack-create-vm-template/6.PNG)

7. 选择要使用然后创建或选择现有的资源组名称的订阅。 然后，选择**创建**启动模板部署。

    ![选择订阅](media/azure-stack-create-vm-template/7.PNG)

8. 资源组部署需要数分钟的时间来创建基于模板的自定义 VM。 可以通过通知和资源组属性来监视安装状态。

    ![部署](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > 部署完成后，VM 将处于运行状态。

## <a name="start-minikube-and-install-an-application"></a>启动 Minikube 并安装应用程序

成功创建 Linux VM 后，可以登录，以便启动 Minikube 并安装应用程序。

1. 部署完成后，请选择“连接”，以查看要用来连接到 Linux VM 的公共 IP 地址。 

    ![连接](media/azure-stack-create-vm-template/9.PNG)

2. 在提升的命令提示符下运行 **mstsc.exe**，打开远程桌面连接并连接到在上一步发现的 Linux VM 的公共 IP 地址。 当系统提示你登录 xRDP 时，请使用在创建 VM 时指定的凭据。

    ![远程](media/azure-stack-create-vm-template/10.PNG)

3. 打开终端仿真器并输入以下命令以便启动 Minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![运行命令](media/azure-stack-create-vm-template/11.PNG)

4. 打开浏览器并转到 Kubernetes 仪表板地址。 祝贺你，现已使用 Minikube 完成了一个功能完全正常的 Kubernetes 安装！

    ![仪表板](media/azure-stack-create-vm-template/12.PNG)

5. 若要部署示例应用程序，请访问官方 Kubernetes 文档页，并跳过"创建 Minikube 群集"部分，因为你已创建一个。 跳到“创建 Node.js 应用程序”部分，网址为 https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 了解 Azure Stack 快速入门模板
> * 使用自定义 GitHub 模板创建 VM
> * 启动 minikube 并安装应用程序
