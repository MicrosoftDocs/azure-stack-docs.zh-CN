---
title: 在 Azure Stack 中创建和发布市场项 | Microsoft Docs
description: 在 Azure Stack 中创建和发布市场项。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 8c77441f458e87a3b8da60541261338abb78057c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985421"
---
# <a name="create-and-publish-a-marketplace-item"></a>创建和发布市场项目

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="create-a-marketplace-item"></a>创建市场项目

1. 下载 [Azure Gallery Packager 工具](https://www.aka.ms/azurestackmarketplaceitem)和示例 Azure Stack 市场项。
2. 打开示例市场项并重命名 **SimpleVMTemplate** 文件夹。 使用与市场项相同的名称，例如 **Contoso.TodoList**。 此文件夹包含：

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [创建 Azure 资源管理器模板]((/azure/azure-resource-manager/resource-group-authoring-templates) 或从 GitHub 中选择一个模板。 市场项使用此模板来创建资源。

    > [!Note]  
    > 切勿对 Azure 资源管理器模板中的任何机密（例如产品密钥、密码或任何客户身份信息）进行硬编码。 将模板 JSON 文件发布到库中后，无法身份验证即可访问这些文件。 存储中的所有机密 [密钥 Vault]((/azure/azure-resource-manager/resource-manager-keyvault-parameter) 并调用它们从模板中。

4. 若要确保可以成功部署资源，测试与 Microsoft Azure Stack Api 的模板。
5. 如果你的模板依赖于虚拟机映像，请按照说明[向 Azure Stack 中添加虚拟机映像](azure-stack-add-vm-image.md)。
6. 将 Azure Resource Manager 模板保存在 **/Contoso.TodoList/DeploymentTemplates/** 文件夹中。
7. 为市场项选择图标和文本。 将图标添加到 **Icons** 文件夹，并向 **Strings** 文件夹中的 **resources** 文件添加文本。 为图标使用 **small**、**medium**、**large** 和 **wide** 命名约定。 有关这些大小的详细说明，请参阅[市场项 UI 参考](#reference-marketplace-item-ui)。

   > [!NOTE]
   > 为正确生成市场项，需要全部四个图标大小（small、medium、large、wide）。
   >
   >
8. 在 **Manifest.json** 文件中，将 **name** 更改为你的市场项的名称。 另外，将 **publisher** 更改为你的公司的名称。
9. 在 **artifacts** 下，将 **name** 和 **path** 更改为你包括的 Azure 资源管理器模板的正确信息：

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. 将 **My Marketplace Items** 替换为你的市场项应当显示在其中的类别的列表：

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. 若要进一步编辑 Manifest.json，请参阅[参考：市场项 manifest.json](#reference-marketplace-item-manifestjson)。

12. 若要将文件夹打包到 .azpkg 文件，请打开命令提示符并运行以下命令：

    ```shell
    AzureGalleryPackager.exe package -m <path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > 输出包的完整路径必须存在。 例如，如果输出路径为 C:\MarketPlaceItem\yourpackage.azpkg，则文件夹 C:\MarketPlaceItem 必须存在。
    >
    >

## <a name="publish-a-marketplace-item"></a>发布市场项

1. 使用 PowerShell 或 Azure 存储资源管理器将市场项 (.azpkg) 上传到 Azure Blob 存储。 可以上传到本地 Azure Stack 存储或上传到 Azure 存储，即包的临时位置。 请确保 blob 可公开访问。
2. 在客户端虚拟机在 Microsoft Azure Stack 环境中，确保使用你的服务管理员凭据设置 PowerShell 会话。 可以在[使用 PowerShell 部署模板](../user/azure-stack-deploy-template-powershell.md)中找到有关如何在 Azure Stack 中对 PowerShell 进行身份验证的说明。
3. 使用 [PowerShell 1.3.0](azure-stack-powershell-install.md) 或更高版本时，可以使用 **Add-AzsGalleryItem** PowerShell cmdlet 将市场项发布到 Azure Stack。 在使用 PowerShell 1.3.0 之前，请使用 cmdlet **Add-AzureRMGalleryitem** 取代 **Add-AzsGalleryItem**。 例如，使用 PowerShell 1.3.0 或更高版本时：

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | 参数 | 描述 |
   | --- | --- |
   | 订阅 ID |管理员订阅 ID。 可以使用 PowerShell 来检索它。 如果希望在门户中获取它，请转到提供商订阅并复制订阅 ID。 |
   | GalleryItemUri |已上传到存储的库包的 Blob URI。 |
   | Apiversion |设置为 **2015-04-01**。 |

4. 转到门户。 你现在可以作为操作员或用户在门户中查看市场项。 包可能需要过几分钟才会出现。

5. 你的市场项现在已保存到 Azure Stack 市场。 你可以选择从 Blob 存储位置中将其删除。
    > [!Caution]  
    > 现在，无需身份验证，即可通过以下 URL 访问所有默认的库项目和自定义库项目：  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. 可以使用 **Remove-AzureRMGalleryItem** cmdlet 删除市场项。 例如：

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > 删除某个项后，市场 UI 可能会显示错误。 若要修复此错误，请在门户中单击“设置”。 然后，在“门户自定义”下选择“放弃修改”。
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>参考：市场项 manifest.json

### <a name="identity-information"></a>标识信息

| 名称 | 需要 | Type | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| 名称 |X |String |[A-Za-z0-9]+ | |
| 发布者 |X |String |[A-Za-z0-9]+ | |
| Version |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>元数据

| 名称 | 需要 | Type | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |建议不要超过 80 个字符 |如果长度超过 80 个字符，门户可能无法美观地显示项名称。 |
| PublisherDisplayName |X |String |建议不要超过 30 个字符 |如果长度超过 30 个字符，门户可能无法美观地显示发布者名称。 |
| PublisherLegalName |X |String |最多 256 个字符 | |
| 摘要 |X |String |60 到 100 个字符 | |
| LongSummary |X |String |140 到 256 个字符 |在 Azure Stack 中尚不适用。 |
| 描述 |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 到 5,000 个字符 | |

### <a name="images"></a>映像

市场使用以下图标：

| 名称 | 宽度 | 高度 | 说明 |
| --- | --- | --- | --- |
| 宽型 |255 px |115 px |始终必需 |
| 大型 |115 px |115 px |始终必需 |
| 中型 |90 px |90 px |始终必需 |
| 小型 |40 px |40 px |始终必需 |
| 屏幕快照 |533 px |32 px |可选 |

### <a name="categories"></a>Categories

应当为每个市场项标记一个类别，该类别标识在门户 UI 中的何处显示该项。 可以选择 Azure Stack 中的现有类别之一（计算、数据 + 存储，等等），也可以选择新建一个。

### <a name="links"></a>链接

每个市场项可以包括指向其他内容的各种链接。 链接以名称和 URI 的列表形式进行指定：

| 名称 | 需要 | Type | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 64 个字符 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>其他属性

除了前面的元数据之外，市场作者可以采用以下形式提供自定义键/值对数据：

| 名称 | 需要 | Type | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 25 个字符 | |
| 值 |X |String |最多 30 个字符 | |

### <a name="html-sanitization"></a>HTML 清理

对于允许使用 HTML 的任何字段，将允许使用以下元素和属性：

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>参考：市场项 UI

在 Azure Stack 门户中显示的市场项的图标和文本将如下所示。

### <a name="create-blade"></a>“创建”边栏选项卡

![“创建”边栏选项卡](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>市场项详细信息边栏选项卡

![市场项详细信息边栏选项卡](media/azure-stack-create-and-publish-marketplace-item/image3.png)
