---
title: 在 Azure Stack Hub 中准备更新包
description: 了解如何在 Azure Stack Hub 中准备更新包。
author: sethmanheim
ms.topic: how-to
ms.date: 02/08/2021
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: b3d1f3d76d988be3647a1d26fec0a763a681d4cf
ms.sourcegitcommit: 824fd33fd5d6aa0c0dac06c21b592bdb60378940
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99850915"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>准备 Azure Stack Hub 更新包

本文概述如何准备 Azure Stack Hub 更新包，以便能够使用这些包来更新 Azure Stack Hub 环境。 此过程包括以下步骤：

- [下载更新包](#download-the-update-package)。
- [使用 Azure Stack Hub 管理员门户将更新包导入 Azure Stack Hub 环境](#import-and-install-updates)。

在可连接到自动更新终结点的系统上，将自动下载并准备 Azure Stack Hub 软件更新和修补程序。 在未建立连接的系统上，若要获取原始设备制造商 (OEM) 的任何更新，必须根据本文所述准备更新包。  

下表显示了何时需要手动准备更新包，以及何时会自动准备更新包。

| 更新类型 | 连接 | 所需的操作 |
| --- | --- | --- |
| Azure Stack Hub 软件更新 | 已连接 | 应用更新时，将自动下载并准备更新。 |
| Azure Stack Hub 修补程序 | 已连接 | 应用更新时，将自动下载并准备更新。 |
| OEM 包更新 | 已连接 | 必须准备更新包。 遵循本文中的步骤。 |
| Azure Stack Hub 软件更新 | 断开连接或连接质量很差 | 必须准备更新包。 遵循本文中的步骤。 |
| Azure Stack Hub 修补程序 | 断开连接或连接质量很差 | 必须准备更新包。 遵循本文中的步骤。 |
| OEM 包更新 | 断开连接或连接质量很差 | 必须准备更新包。 遵循本文中的步骤。 |

## <a name="download-the-update-package"></a>下载更新包

Azure Stack Hub 更新和修补程序的更新包可通过门户中的更新边栏选项卡供已连接系统使用。 如果要更新某个 OEM 包，或要支持已断开连接的系统，则下载包并将其移到 Azure Stack Hub 实例可访问的位置。 如果运行间歇性连接的系统，则可能也需要下载包，然后将其上传到可访问的位置。

查看包的内容。 更新包通常包含以下文件：

- 一个自解压缩 \<PackageName>.zip 文件。 此文件包含更新的有效负载。
- **Metadata.xml 文件**。 此文件包含有关更新的基本信息，例如发布者、名称、先决条件、大小和支持路径 URL。

### <a name="automatic-download-and-preparation-for-update-packages"></a>自动下载和准备更新包

对于已连接到 Azure Stack Hub 自动更新终结点的系统，系统会自动准备 Azure Stack Hub 软件更新和修补程序：`https://*.azureedge.net` 和 `https://aka.ms/azurestackautomaticupdate`。 若要详细了解如何与 Azure Stack Hub 自动更新终结点建立连接，请参阅 [Azure Stack Hub 防火墙集成](./azure-stack-integrate-endpoints.md#ports-and-urls-outbound)中所述的“修补升级”终结点 。

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Azure Stack Hub 更新包的下载位置

用于[完整和快速更新](./azure-stack-updates.md#update-package-types)的 Azure Stack Hub 更新托管在安全的 Azure 终结点上。 当更新可用时，使用已连接的实例的 Azure Stack Hub 操作员会看到 [Azure Stack Hub 更新自动出现在管理员门户中](#automatic-download-and-preparation-for-update-packages)。 对于离线系统或者 Internet 连接质量很差的系统，可以使用 [Azure Stack Hub 更新下载器工具](https://aka.ms/azurestackupdatedownload)下载更新包。 Azure Stack Hub 软件更新包可能包含 Azure Stack Hub 服务的更新，以及 Azure Stack Hub 缩放单元的操作系统更新。

>[!NOTE]
>更新包本身及其内容（例如二进制文件、PowerShell 脚本等）都已通过 Microsoft 自有的证书进行签名。 篡改该包会使签名失效。

### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Azure Stack Hub 修补程序包的下载位置

[Azure Stack Hub 修补程序](./azure-stack-updates.md#update-package-types)的包托管于 Azure Stack Hub 更新所在的安全 Azure 终结点中。 使用已连接的实例的 Azure Stack Hub 操作员会看到 [Azure Stack Hub 更新自动出现在管理员门户中](#automatic-download-and-preparation-for-update-packages)（当更新可用时）。 可以使用各个修补程序知识库文章中的嵌入式链接下载它们。 你还可以在与 Azure Stack 中心版本对应的发行说明中找到修补程序知识库文章的链接。

### <a name="where-to-download-oem-update-packages"></a>OEM 更新包的下载位置

OEM 供应商可能还会发布更新，例如驱动程序和固件更新。 尽管这些更新是硬件供应商作为单独的 [OEM 包更新](./azure-stack-updates.md#update-package-types)交付的，但它们的导入、安装和管理方式与 Microsoft 提供的更新包相同。 可以在[应用 Azure Stack Hub OEM 更新](./azure-stack-update-oem.md#oem-contact-information)一文中找到供应商联系方式链接列表。

## <a name="import-and-install-updates"></a>导入并安装更新

以下过程说明如何在管理员门户中导入并安装更新包。

> [!IMPORTANT]  
> 请向用户通知任何维护操作，并确保尽可能将正常维护时段安排在非营业时间。 维护操作可能会同时影响用户工作负荷和门户操作。

1. 在管理员门户中，选择“所有服务”。 然后，在“数据 + 存储”类别下，选择“存储帐户”。 或者，在筛选框中开始键入“存储帐户”，然后选择它。

    ![Azure Stack Hub 更新](./media/azure-stack-update-prepare-package/image1.png)

2. 在筛选框中，键入“更新”，然后选择 **updateadminaccount** 存储帐户。

3. 在存储帐户详细信息中，在“服务”下，选择 **Blob**。

    ![Azure Stack Hub 更新 - Blob](./media/azure-stack-update-prepare-package/image2.png)

4. 在“Blob 服务”下，选择“+ 容器”以创建容器。  输入名称（例如 update-1811），然后选择“确定” 。

    ![Azure Stack Hub 更新 - 容器](./media/azure-stack-update-prepare-package/image3.png)

5. 创建容器后，选择容器名称，然后选择“上传”将包文件上传到容器。

    ![Azure Stack Hub 更新 - 上传](./media/azure-stack-update-prepare-package/image4.png)

6. 在“上传 Blob”下，选择文件夹图标，浏览到更新包 .zip 文件，然后在文件资源管理器窗口中选择“打开” 。

7. 在“上传 blob”下，选择“上传” 。

    ![Azure Stack Hub 更新 - 上传 Blob](./media/azure-stack-update-prepare-package/image5.png)

8. 对于更新包中的 **Metadata.xml** 文件和其他任何 .zip 文件，请重复步骤 6 和 7。 不要导入 **Supplemental Notice.txt** 文件（如果已包含）。

9. 完成后，可以查看通知（选择门户右上角的钟形图标）。 通知应指示上传已完成。

10. 转回到仪表板上的“更新”边栏选项卡。 该边栏选项卡应指示有可用更新。 这表示更新已成功完成。 选择该边栏选项卡可查看新添加的更新包。

11. 若要安装更新，请选择标记为“就绪”的包，然后右键单击该包并选择“立即更新”，或者选择顶部附近的“立即更新”操作  。

12. 选择正在安装的更新包时，可以在“更新运行详细信息”区域中查看状态。 还可以在此处选择“下载摘要”以下载日志文件。 更新运行日志在尝试结束后六个月内可用。

13. 完成更新后，“更新”边栏选项卡将显示更新后的 Azure Stack Hub 版本。

在 Azure Stack Hub 上安装更新后，可以手动从存储帐户中删除更新。 Azure Stack Hub 会定期检查是否有旧版更新包并将其从存储中删除。 Azure Stack Hub 可能需要长达两周的时间来删除旧包。

## <a name="next-steps"></a>后续步骤

[应用更新](azure-stack-apply-updates.md)
