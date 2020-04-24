---
title: 准备 Azure Stack Hub 更新包
description: 了解如何准备 Azure Stack Hub 更新包。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 1911a07c1a22cbb58b7b7794addadb9cf01f0993
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "78367329"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>准备 Azure Stack Hub 更新包

本文概述如何准备 Azure Stack Hub 更新包，以便能够使用这些包来更新 Azure Stack Hub 环境。 此过程包括以下步骤：

- [下载更新包](#download-the-update-package)
- [通过 Azure Stack Hub 管理员门户将更新包导入 Azure Stack Hub 环境](#import-and-install-updates)

在可连接到自动更新终结点的系统上，将自动下载并准备 Azure Stack Hub 软件更新和修补程序。 在未建立连接的系统上，若要获取 OEM 的任何更新，必须根据本主题所述准备更新包。  

下表显示了何时需要手动准备更新包，以及何时会自动准备更新包。

| 更新类型 | 连接 | 所需的操作 |
| --- | --- | --- |
| Azure Stack Hub 软件更新 | 连续 | 应用更新时，将自动下载并准备更新。 |
| Azure Stack Hub 修补程序 | 连续 | 应用更新时，将自动下载并准备更新。 |
| OEM 包更新 | 连续 | 必须准备更新包。 遵循本文中的步骤。 |
| Azure Stack Hub 软件更新 | 断开连接或连接质量很差 | 必须准备更新包。 遵循本文中的步骤。 |
| Azure Stack Hub 修补程序 | 断开连接或连接质量很差 | 必须准备更新包。 遵循本文中的步骤。 |
| OEM 包更新 | 断开连接或连接质量很差 | 必须准备更新包。 遵循本文中的步骤。 |

## <a name="download-the-update-package"></a>下载更新包
Azure Stack Hub 更新和修补程序的更新包可通过已连接系统的更新边栏选项卡获取。 如果要更新某个 OEM 包，或要支持已断开连接的系统，则需要下载包并将其移到 Azure Stack Hub 实例可访问的位置。 如果运行间歇性连接的系统，则可能也需要下载包，然后将其上传到可访问的位置。

查看包的内容。 更新包通常包含以下文件：

-   **自解压 \<包名称>.exe 文件**。 此文件包含更新的有效负载。
- **Metadata.xml 文件**。 此文件包含有关更新的基本信息，例如发布者、名称、先决条件、大小和支持路径 URL。

### <a name="automatic-download-and-preparation-for-update-packages"></a>自动下载和准备更新包
对于已连接到 **Azure Stack Hub 自动更新终结点**的系统，系统会自动准备 Azure Stack Hub 软件更新和修补程序： https://*.azureedge.net 和 https://aka.ms/azurestackautomaticupdate 。 有关与 **Azure Stack Hub 自动更新终结点**建立连接的详细信息，请参阅 **Azure Stack Hub 防火墙集成**中所述的[修补升级](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound)终结点

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Azure Stack Hub 更新包的下载位置

用于[完整和快速更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types)的 Azure Stack Hub 更新托管在安全的 Azure 终结点上。 使用已连接的实例的 Azure Stack Hub 操作员将看到 [Azure Stack Hub 更新自动出现在管理门户中](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages)。 对于已断开 Internet 连接的系统或者 Internet 连接质量很差的系统，可以使用 [Azure Stack Hub 更新下载器工具](https://aka.ms/azurestackupdatedownload)下载更新包。 Azure Stack Hub 软件更新包可能包含 Azure Stack Hub 服务的更新，以及 Azure Stack Hub 缩放单元的操作系统更新。

>[!NOTE]
>更新包本身及其内容（例如二进制文件、PowerShell 脚本等）都已通过 Microsoft 自有的证书进行签名。 篡改该包会使签名失效。


### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Azure Stack Hub 修补程序包的下载位置

[Azure Stack Hub 修补程序](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types)的包托管于 Azure Stack Hub 更新所在的同一个安全 Azure 终结点中。 使用已连接的实例的 Azure Stack Hub 操作员将看到 [Azure Stack Hub 更新自动出现在管理门户中](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages)。 可以使用每篇相应的修补程序知识库文章（例如，[Azure Stack Hub 修补程序 1.1906.11.52](https://support.microsoft.com/help/4515650)）中的嵌入式链接下载它们。 可以在与 Azure Stack Hub 版本对应的发行说明中找到修补程序。

### <a name="where-to-download-oem-update-packages"></a>OEM 更新包的下载位置
OEM 供应商还会发布更新，例如驱动程序和固件更新。 尽管这些更新是硬件供应商作为单独的 [OEM 包更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types)交付的，但它们的导入、安装和管理方式与 Microsoft 提供的更新包相同。 可以在[应用 Azure Stack Hub 原始设备制造商 (OEM) 更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information)中找到供应商联系方式链接列表。

## <a name="import-and-install-updates"></a>导入并安装更新

以下过程说明如何在管理门户中导入并安装更新包。

> [!Important]  
> 请向用户通知任何维护操作，并尽可能将正常维护时段安排在非工作时间。 维护操作可能会同时影响用户工作负荷和门户操作。

1.  在管理门户中，选择“所有服务”。  然后，在“数据 + 存储”  类别下，选择“存储帐户”  。 （或者，在筛选框中开始键入“存储帐户”  ，然后选择它。）

    ![Azure Stack Hub 更新](./media/azure-stack-update-prepare-package/image1.png) 

1.  在筛选框中，键入“更新”  ，然后选择 **updateadminaccount** 存储帐户。

2.  在存储帐户详细信息中，在“服务”  下，选择 **Blob**。

    ![Azure Stack Hub 更新 - Blob](./media/azure-stack-update-prepare-package/image2.png)

1.  在“Blob 服务”下，选择“+ 容器”以创建容器。   输入名称（例如 *update-1811*），然后选择“确定”  。

    ![Azure Stack Hub 更新 - 容器](./media/azure-stack-update-prepare-package/image3.png)

1.  创建容器后，单击容器名称，然后单击“上传”  将包文件上传到容器。

    ![Azure Stack Hub 更新 - 上传](./media/azure-stack-update-prepare-package/image4.png)

1.  在“上传 Blob”下，单击文件夹图标，浏览到更新包的 .zip 文件，然后在文件资源管理器窗口中单击“打开”。  

2.  在“上传 blob”  下，单击“上传”  。

    ![Azure Stack Hub 更新 - 上传 Blob](./media/azure-stack-update-prepare-package/image5.png)

1.  对于更新包中的 Metadata.xml 文件和其他任何 .zip 文件，请重复步骤 6 和 7。 不要导入 Supplemental Notice.txt 文件（如果已包含）。

2.  完成后，可以查看通知（在门户右上角的钟形图标）。 通知应指示已完成上传。

3.  导航回到仪表板上的“更新”边栏选项卡。 该边栏选项卡应指示有可用更新。 这表示更新已成功完成。 单击该边栏选项卡可查看新添加的更新包。

4.  若要安装更新，请选择标记为“就绪”  的包，然后右键单击该包并选择“立即更新”  ，或者单击顶部附近的“立即更新”  操作。

5.  单击正在安装的更新包时，可以在“更新运行详细信息”  区域中查看状态。 还可以在此处单击“下载摘要”  以下载日志文件。 更新运行日志在尝试结束后 6 个月内可用。

6.  完成更新后，“更新”边栏选项卡将显示更新后的 Azure Stack Hub 版本。

在 Azure Stack Hub 上安装更新后，可以手动从存储帐户中删除更新。 Azure Stack Hub 会定期检查是否有旧版更新包并将其从存储中删除。 Azure Stack Hub 可能需要花费两周时间来删除旧包。

## <a name="next-steps"></a>后续步骤

[应用更新](azure-stack-apply-updates.md)
