---
title: 如何在 Azure Stack 集线器上安装 IoT 中心
description: 了解如何在 Azure Stack 集线器上安装 IoT 中心资源提供程序。
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 13bac36ffd7d771d1b5504067bfe1d679cdda5e6
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049722"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>如何在 Azure Stack 集线器上安装 IoT 中心

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

本文介绍如何下载和安装 IoT 中心资源提供程序，使其可用于订阅的客户。 IoT 中心的安装过程耗时大约2小时。

## <a name="download-iot-hub"></a>下载 IoT 中心

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
如果 Azure Stack 中心可以访问 Azure Marketplace，请按照本部分中的步骤下载并安装 IoT 中心及其依赖项。 

若要下载用于已连接部署的 IoT 中心，请完成以下步骤：

1. 登录到 Azure Stack 中心管理员门户。 
2. 选择左侧的 " **Marketplace 管理** "，选择 " **资源提供程序** "，然后单击 " **从 Azure 添加** "。

    [![Marketplace RP 搜索](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. 筛选 "IoT 中心" （如有必要），然后选择 " **Iot 中心** " 包。

    [![Marketplace IoT 中心 RP](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. 在 " **IoT 中心** 包" 页面上，选择 " **下载** "

    [![IoT 中心包详细信息](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. 等待包下载完成。 状态将显示 **下载** ，可能需要长达10分钟的时间。

    [![IoT 中心包下载状态](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. 下载包后，" **Marketplace 管理** " 页中的状态将更改为 " **未安装** "。

    [![未安装 IoT 中心包下载](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
若要下载已断开连接或部分连接的部署的 IoT 中心，请首先将包下载到本地计算机。 下载完成后，将其导入 Azure Stack 中心实例。

1. 如果尚未这样做，请按照[下载市场项 - 离线或部分联网的场景](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)中的说明进行操作。 在此处下载并运行 Marketplace 联合工具，该工具允许你下载 IoT 中心包。
2. 在联合工具的 "Azure Marketplace 项" 窗口打开后，查找并选择 "IoT 中心"，将所需的包下载到你的本地计算机。
3. 下载完成后，将包导入到 Azure Stack Hub 实例，然后发布到市场。
::: zone-end

## <a name="install-iot-hub"></a>安装 IoT 中心

在 **Marketplace 管理** 页上，按照以下步骤安装 IoT 中心包：

1. 单击 " **IoT 中心** " 行，然后 **开始安装** 。

    [![IoT 中心 RP 正在等待安装](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. 单击 " **安装必备组件** "。

    [![IoT 中心 RP 安装必备组件](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. 可以在 "通知" 窗格中跟踪安装状态。 此步骤需要大约10分钟。

    [![IoT 中心 RP 安装必备组件安装](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. 等待安装必备组件完成。 此步骤通常会在5-10 分钟内完成，具体取决于环境。

    [![IoT 中心 RP 准备密码](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. 单击 " **准备密码** " 下的 " **添加证书** "。 提供在先决条件中创建的 pfx 证书。

    [![IoT 中心 RP 准备密钥-上载证书](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. 浏览并提供所创建的 pfx，并提供脚本输入 (密码) 

    [![IoT 中心 RP 准备密码-选取证书](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![IoT 中心 RP 准备机密-提供密码](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. 单击 " **安装资源提供程序** " 下的 " **安装** "。

    [![IoT 中心 RP 准备密码-完成](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. 安装开始后，可在 Marketplace 或通知窗格中找到部署状态。

    [![IoT 中心 RP 安装正在进行中](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. 安装过程可能需要90到120分钟。 等待安装完成。

    [![IoT 中心 RP 安装完成](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![Marketplace RPs-已安装 RPs](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

已成功安装 IoT 中心资源提供程序！ 若要开始操作，请完成以下步骤：

1. 如有必要，请按照说明 [创建计划、产品/服务和订阅](azure-stack-plan-offer-quota-overview.md)。

2. 如果订阅已存在，则更新关联的产品/计划以包括 **Microsoft. 设备** 服务。 "中转到 **计划**  ->  **" 选择计划以更新 "**  ->  **添加服务和配额** "。

3. 添加 **""，然后** 单击 " **保存** "。

    [![向计划添加 IoT 中心服务](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. 已设置完！ 现在可以创建 IoT 中心。

## <a name="using-iot-hub"></a>使用 IoT 中心

若要了解如何使用 IoT 中心，请参阅 [Azure Iot 中心文档](/azure/iot-hub)。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure Stack 集线器上管理 IoT 中心，请参阅 [如何在 Azure Stack 中心管理 Iot 中心](iot-hub-rp-manage.md)。
