---
title: 创建 OEM 扩展包
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 中创建 OEM 扩展包。
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 61e71e891f68118a89f0295528f6aab21507da1c
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625469"
---
# <a name="create-an-oem-package"></a>创建 OEM 包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack 中心 OEM 扩展包是将 OEM 特定内容添加到 Azure Stack 中心基础结构的机制。 内容用于部署和操作过程，如更新、扩展和现场替换。

## <a name="creating-the-package"></a>创建包

创建并验证后，可以在 VaaS 中使用 OEM 扩展包。 在继续之前，请确保已完成[创建 OEM 包](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)的步骤。 然后，将包提交给 Microsoft，并在包验证工作流中对 VaaS 测试结果进行签名。 以下步骤详细说明了如何将生成的文件捆绑到 VaaS 可使用的单个 zip 文件中。

1. 标识包的以下内容：
    - 包含包内容的 zip 文件。
    - 名为 `oemMetadata.xml`的清单文件，其内容与包内容的根目录中的 `metadata.xml` 文件的内容相同。

2. 选择 "内容文件" 并创建一个 zip 文件：

    创建 oem 扩展包时 ![Zip 文件内容](media/vaas-create-oem-package-1.png) ![在创建 OEM 扩展包时压缩项内容](media/vaas-create-oem-package-2.png)

3. 重命名生成的文件，使其具有足够的描述性来识别它。

## <a name="verifying-the-contents"></a>验证内容

若要验证 zip 文件的结构，请检查该文件，并检查是否没有子文件夹。 TLD 具有压缩内容。 有效的包结构如下所示：

> [!IMPORTANT]
> 压缩父文件夹而不是内容将导致包签名失败。

![创建 OEM 扩展包时正确压缩包内容](media/vaas-create-oem-package-3.png)

现在，可以在包验证工作流中将 zip 文件上传到 VaaS 并由 Microsoft 签名。

## <a name="next-steps"></a>后续步骤

- [验证 OEM 包](azure-stack-vaas-validate-oem-package.md)
