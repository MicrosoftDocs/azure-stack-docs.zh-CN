---
title: 如何在 Azure Stack Hub 上安装 IoT 中心
description: 了解如何在 Azure Stack Hub 上安装 IoT 中心资源提供程序。
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 47d06bc7363a9ce8d4de8971bf26c38bfdb16bcc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256109"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>如何在 Azure Stack Hub 上安装 IoT 中心

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

本文介绍如何下载和安装 IoT 中心资源提供程序，使其可供客户订阅。 IoT 中心的安装过程大约需要 2 小时。

## <a name="download-iot-hub"></a>下载 IoT 中心

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
如果 Azure Stack Hub 可以访问 Azure 市场，请按照本部分中的步骤下载并安装 IoT 中心及其依赖项。 

若要为连接的部署下载 IoT 中心，请完成以下步骤：

1. 登录到 Azure Stack Hub 管理员门户。 
2. 选择左侧的“市场管理”，然后选择“资源提供程序”，接着单击“+ 从 Azure 添加”  。

    [![市场 RP 搜索](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. 筛选“IoT 中心”（如有必要），然后选择“IoT 中心”包。

    [![市场 IoT 中心 RP](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. 在“IoT 中心”包页上，选择“下载” 

    [![IoT 中心包详细信息](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. 等待包下载完成。 状态将显示“正在下载”，最多可能需要 10 分钟时间。

    [![IoT 中心包“正在下载”状态](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. 包下载完成后，“市场管理”页中的状态将更改为“未安装” 。

    [![IoT 中心包已下载但尚未安装](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
若要为断开连接的部署或部分连接的部署下载 IoT 中心，需要先将包下载到本地计算机。 下载完成后，将其导入 Azure Stack Hub 实例。

1. 如果尚未这样做，请按照[下载市场项 - 离线或部分联网的场景](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)中的说明进行操作。 其中，你将下载并运行“市场联合”工具，该工具用于下载 IoT 中心包。
2. 在该联合工具的“Azure 市场项”窗口打开后，查找并选择“IoT 中心”，将所需的包下载到本地计算机。
3. 下载完成后，将包导入到 Azure Stack Hub 实例，然后发布到市场。
::: zone-end

## <a name="install-iot-hub"></a>安装 IoT 中心

在“市场管理”页上，按照以下步骤安装 IoT 中心包：

1. 单击“IoT 中心”行，然后单击“开始安装” 。

    [![IoT 中心 RP“正在等待安装”](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. 单击“安装必备组件”。

    [![IoT 中心 RP“安装必备组件”](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. 可在通知窗格中跟踪安装状态。 此步骤需要大约 10 分钟时间。

    [![IoT 中心 RP“安装必备组件”- 正在安装](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. 等待必备组件安装完成。 此步骤通常需要 5-10 分钟时间，具体取决于环境。

    [![IoT 中心 RP“准备机密”](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. 单击“准备机密”下的“添加证书” 。 提供在必备组件中创建的 pfx 证书。

    [![IoT 中心 RP“准备机密”- 上传证书](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. 浏览并提供所创建的 pfx 和密码（脚本的输入）

    [![IoT 中心 RP“准备机密”- 选取证书](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![IoT 中心 RP“准备机密”- 提供密码](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. 单击“安装资源提供程序”下的“安装” 。

    [![IoT 中心 RP“准备机密”- 完成](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. 开始安装后，可在市场或通知窗格中找到部署状态。

    [![IoT 中心 RP“正在进行安装”](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. 安装可能需要 90-120 分钟的时间。 等待安装完成。

    [![IoT 中心 RP“安装完成”](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![市场 RP - 已安装 RP](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

已成功安装 IoT 中心资源提供程序！ 若要开始操作，请完成以下步骤：

1. 如有必要，请按照说明[创建计划、套餐和订阅](./service-plan-offer-subscription-overview.md)。

2. 如果订阅已存在，请更新关联的套餐/计划以包括 Microsoft.Devices 服务。 转到“计划” -> “选择要更新的计划” -> “添加服务和配额”  。

3. 添加“Microsoft.Devices 服务”，然后单击“保存” 。

    [![将 IoT 中心服务添加到计划](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. 一切就绪！ 现在即可创建 IoT 中心。

## <a name="using-iot-hub"></a>使用 IoT 中心

若要了解如何使用 IoT 中心，请参阅 [Azure IoT 中心文档](/azure/iot-hub)。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure Stack Hub 上管理 IoT 中心，请参阅[如何在 Azure Stack Hub 上管理 IoT 中心](iot-hub-rp-manage.md)。