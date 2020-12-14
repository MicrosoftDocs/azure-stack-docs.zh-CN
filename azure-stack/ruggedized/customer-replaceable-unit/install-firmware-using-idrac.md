---
title: 使用 iDRAC 安装固件
description: 了解如何使用 iDRAC 安装固件
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5b42b0a1f6be6e9fdf8110854e37f602d25b18ad
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390667"
---
# <a name="installing-firmware-using-the-idrac-interface"></a>使用 iDRAC 接口安装固件

利用集成的 Dell 远程访问卡 (iDRAC) ，你可以使用 **更新和回滚** 功能，以远程方式 (每) 个固件来远程更新固件。 即使服务器正在运行，也是如此。

请访问 [14G 中心的集成 Microsoft Azure Stack 系统文档](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs) 页，下载最新的支持矩阵，其中包含一系列受支持的固件版本。

使用以下过程，通过 iDRAC web 界面更新单个设备固件。

**步骤**

1.  请参阅 **维护** \* * 系统更新 * *。 随即显示 " **固件更新** " 页。

    ![](media/image-85.png)

2.  在 " **更新** " 选项卡中，选择 " **本地** " 作为文件位置。

3.  选择 " **浏览**"，选择所需组件的固件映像，然后选择 " **上传**"。

4.  上传完成后，" **更新详细信息** " 部分会显示上传到 iDRAC 及其状态的每个固件文件。 如果固件映像文件有效且已成功上传，则 " **内容** " 列将显示固件映像文件旁边的 (+) 图标。 展开名称，查看 **设备名称**、 **当前** 和 **可用固件版本** 信息。

5.  选择所需的固件文件，然后执行下列操作之一：

    -   对于不需要重新启动主机系统的固件映像，请选择 " **安装**"。

    -   对于需要重新启动主机系统的固件映像，请选择 " **安装并重新启动** " 或 " **安装下次重新启动**"。

    -   若要取消固件更新，请选择 " **取消**"。

6.  若要显示 " **作业队列** " 页，请选择 " **作业队列**"。 使用此页可以查看和管理分段固件更新，或选择

    **"确定"** 以刷新当前页面并查看固件更新状态。
    
