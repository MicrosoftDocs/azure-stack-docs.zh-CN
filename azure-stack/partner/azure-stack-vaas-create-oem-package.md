---
title: Azure Stack 验证最佳做法 |Microsoft Docs
description: 本文介绍了使用验证作为服务的最佳做法。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2ee5a873a6281ed5d71db05b05abd15986043676
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167369"
---
# <a name="create-an-oem-package"></a>创建 OEM 包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack OEM 扩展包是一种机制，通过该机制，可以将 OEM 特定内容添加到 Azure Stack 基础结构，以便在部署中使用，也可以在更新、扩展和现场替换等操作过程中使用。

## <a name="creating-the-package"></a>创建包

创建并验证后，可以在 VaaS 中使用 OEM 扩展包。  在继续之前，请确保已完成[创建 OEM 包](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)的步骤。 然后，将包提交给 Microsoft，并在包验证工作流中对 VaaS 测试结果进行签名。 以下步骤详细说明了如何将生成的文件捆绑到 VaaS 可使用的单个 zip 文件中。

1. 标识包的以下内容：
    - 包含包内容的 zip 文件
    - 名为 `oemMetadata.xml`的清单文件，其内容应与包内容的根目录中的元数据 .xml 文件的内容相同。

2. 选择内容文件，并从内容创建 zip 文件：

    ![Zip 文件内容](media/vaas-create-oem-package-1.png) ![压缩项内容](media/vaas-create-oem-package-2.png)

3. 重命名所生成的文件，使其具有足够的描述性来识别它。

## <a name="verifying-the-contents"></a>验证内容

若要验证 zip 文件的结构，请检查该文件，并检查是否没有子文件夹。 TLD 具有压缩内容。 下面显示了有效的包结构。
> [!IMPORTANT]
> 压缩父文件夹而不是内容将导致包签名失败。

![正确压缩包内容](media/vaas-create-oem-package-3.png)

现在，可以在包验证工作流中将 zip 文件上传到 VaaS 并由 Microsoft 签名。

## <a name="next-steps"></a>后续步骤

- [验证 OEM 包](azure-stack-vaas-validate-oem-package.md)
