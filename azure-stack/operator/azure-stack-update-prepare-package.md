---
title: 准备 Azure Stack 更新包 |Microsoft Docs
description: 了解如何准备 Azure Stack 更新包。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: ab7b764e608ed1fb8008071296d0004f6ef65e7a
ms.sourcegitcommit: 1c45814696e70ba987dd39ce61d93ea4ef5222ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70029482"
---
# <a name="prepare-an-azure-stack-update-package"></a>准备 Azure Stack 更新包

适用范围：*Azure Stack 集成系统*

本文概述了如何准备 Azure Stack 更新包, 以便将其用于更新 Azure Stack 环境。 此过程包括:

- [下载更新包](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#download-the-update-package)
- [通过 Azure Stack 管理员门户将更新包导入 Azure Stack 环境](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#import-and-install-updates)

此过程是自动完成的, 以便将 connectivty 的系统中的软件更新和修补程序 Azure Stack 到[Azure Stack 自动更新终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages)。

下表显示更新包何时需要手动准备并自动准备:

| 更新类型 | Azure Stack 环境与[Azure Stack 自动更新终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages)的连接 | 所需操作 |
| --- | --- | --- |
| Azure Stack 软件更新 | 已连接 | 应用更新时, 将自动下载并准备更新。 |
| Azure Stack 修补程序 | 已连接 | 应用更新时, 将自动下载并准备更新。 |
| OEM 包更新 | 已连接 | 必须准备更新包。 请按照本文中的步骤进行操作。 |
| Azure Stack 软件更新 | 断开连接或连接弱 | 必须准备更新包。 请按照本文中的步骤进行操作。 |
| Azure Stack 修补程序 | 断开连接或连接弱 | 必须准备更新包。 请按照本文中的步骤进行操作。 |
| OEM 包更新 | 断开连接或连接弱 | 必须准备更新包。 请按照本文中的步骤进行操作。 |

## <a name="download-the-update-package"></a>下载更新包
Azure Stack 更新和修补程序的更新包可通过连接的系统的 "更新" 边栏选项卡获取。 如果要更新 OEM 包或支持已断开连接的系统, 则需要下载包并将包移到 Azure Stack 实例可访问的位置。 如果正在运行具有间歇连接的系统, 则可能还需要下载包并将其上传到可访问位置。

查看包内容。 更新包通常包含以下文件：

-   **自解压缩\<PackageName > .zip 文件**。 此文件包含更新的有效负载。
- **一个元数据 .xml 文件**。 此文件包含有关更新的重要信息, 例如, 发布者、名称、先决条件、大小和支持路径 URL。

### <a name="automatic-download-and-preparation-for-update-packages"></a>自动下载和准备更新包
为连接到**Azure Stack 自动更新终结点**的系统自动准备 Azure Stack 软件更新和修补程序: https://endpointname>.azureedge.net 和 https://aka.ms/azurestackautomaticupdate 。 有关设置与**Azure Stack 自动更新终结点**的连接的详细信息, 请参阅[Azure Stack 防火墙集成](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound)中所述的**修补程序和更新**终结点

### <a name="where-to-download-azure-stack-update-packages"></a>Azure Stack 更新包的下载位置

[完整更新和快速更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types)Azure Stack 的更新托管在安全的 Azure 终结点上。 具有连接的实例的 Azure Stack 操作员会看到[Azure Stack 更新会自动显示在管理门户中](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages)。 对于 internet 断开连接的系统或具有弱 internet 连接的系统, 可以使用[Azure Stack 更新下载程序工具](https://aka.ms/azurestackupdatedownload)下载更新包。 Azure Stack 软件更新包可能包含 Azure Stack 服务的更新, 以及 Azure Stack 的缩放单位的操作系统的更新。

### <a name="where-to-download-azure-stack-hotfix-packages"></a>Azure Stack 修补程序包下载位置

[Azure Stack 修补程序](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types)的包与用于 Azure Stack 更新的安全 Azure 终结点相同。 具有连接的实例的 Azure Stack 操作员会看到[Azure Stack 更新会自动显示在管理门户中](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages)。 你可以使用每个相关修补程序知识库文章中的嵌入链接下载它们;例如, [Azure Stack 修补程序 1.1906.11.52](https://support.microsoft.com/help/4515650)。 你可以在与 Azure Stack 版本对应的发行说明中找到修补程序。OEM 硬件供应商提供的更新

### <a name="where-to-download-oem-update-packages"></a>下载 OEM 更新包的位置
OEM 供应商还会发布更新，例如驱动程序和固件更新。 尽管这些更新作为硬件供应商单独的[OEM 包更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types)传送, 但仍会导入、安装和管理与 Microsoft 的更新包相同的方式。 可以在[应用 Azure Stack 原始设备制造商 (OEM) 更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information)中找到供应商联系人链接列表。

## <a name="import-and-install-updates"></a>导入并安装更新

下面的过程演示如何在管理门户中导入和安装更新包。

> [!Important]  
> 通知用户任何维护操作, 并在非工作时间尽可能地计划正常维护时段。 维护操作可能会同时影响用户工作负荷和门户操作。

1.  在管理门户中, 选择 "**所有服务**"。 然后，在“数据 + 存储”类别下，选择“存储帐户”。 （或者，在筛选框中开始键入“存储帐户”，然后选择它。）

    ![Azure Stack 更新](./media/azure-stack-update-prepare-package/image1.png) 

1.  在筛选框中，键入“更新”，然后选择 **updateadminaccount** 存储帐户。

2.  在存储帐户详细信息中，在“服务”下，选择 **Blob**。

    ![Azure Stack 更新-blob](./media/azure-stack-update-prepare-package/image2.png)

1.  在 " **Blob 服务**" 下, 选择 " **+ 容器**" 创建容器。 输入名称 (例如,*更新-1811*), 然后选择 **"确定"** 。

    ![Azure Stack 更新-容器](./media/azure-stack-update-prepare-package/image3.png)

1.  创建容器后，单击容器名称，然后单击“上传”将包文件上传到容器。

    ![Azure Stack 更新-上传](./media/azure-stack-update-prepare-package/image4.png)

1.  在 "**上传 blob**" 下, 单击 "文件夹" 图标, 浏览到更新包的 .zip 文件, 然后单击 "文件资源管理器" 窗口中的 "**打开**"。

2.  在“上传 blob”下，单击“上传”。

    ![Azure Stack 更新-上传 blob](./media/azure-stack-update-prepare-package/image5.png)

1.  对于 Metadata .xml 文件和更新包中的任何其他 .zip 文件, 请重复步骤6和7。 不要导入 Supplemental Notice.txt 文件（如果已包含）。

2.  完成后，可以查看通知（在门户右上角的钟形图标）。 通知应指示已完成上传。

3.  导航回仪表板上的 "更新" 边栏选项卡。 该边栏选项卡应指示有可用的更新。 这表示更新已成功完成。 单击边栏选项卡以查看新添加的更新包。

4.  若要安装更新，请选择标记为“就绪”的包，然后右键单击该包并选择“立即更新”，或者单击顶部附近的“立即更新”操作。

5.  单击正在安装的更新包时，可以在“更新运行详细信息”区域中查看状态。 你还可以从此处单击 "**下载摘要**" 以下载日志文件。 在尝试结束后, 将在6个月内提供来自更新运行的日志。

6.  更新完成后, "更新" 边栏选项卡会显示更新的 Azure Stack 版本。

在 Azure Stack 上安装更新后，可以手动从存储帐户中删除更新。 Azure Stack 会定期检查是否有旧版更新包并将其从存储中删除。 Azure Stack 可能需要两周时间才能删除旧包。

## <a name="next-steps"></a>后续步骤

[应用更新](azure-stack-apply-updates.md)
