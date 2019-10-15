---
title: 在 Azure Stack 中创建和发布市场项 | Microsoft Docs
description: 了解如何创建和发布 Azure Stack Marketplace 项。
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
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 4a8f24c11f8e72c4b3e2b99ae6b2a417e3bd0cba
ms.sourcegitcommit: 5eae057cb815f151e6b8af07e3ccaca4d8e4490e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72310583"
---
# <a name="create-and-publish-a-custom-azure-stack-marketplace-item"></a>创建和发布自定义 Azure Stack Marketplace 项

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

发布到 Azure Stack Marketplace 的每个项目都使用 Azure 库包（.azpkg）格式。 使用*Azure Gallery Packager*工具，你可以创建自定义 Azure 库包，你可以将其上传到 Azure Stack Marketplace，用户可以下载该包。 部署过程使用 Azure 资源管理器模板。

## <a name="marketplace-items"></a>市场项

本文中的示例演示如何创建一个类型为 Windows 或 Linux 的 VM Marketplace 产品。

## <a name="create-a-marketplace-item"></a>创建市场项目

> [!IMPORTANT]
> 创建 VM marketplace 项之前，请按照[将 vm 映像添加到 Azure Stack](azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal)中的说明，将自定义 VM 映像上传到 Azure Stack 门户。 然后，按照本文中的说明打包映像（创建 .azpkg）并将其上传到 Azure Stack Marketplace。

若要创建自定义 marketplace 项，请执行以下操作：

1. 下载[Azure Gallery Packager 工具](https://aka.ms/azsmarketplaceitem)和示例 Azure Stack 库包。 此下载包括自定义 VM 模板。 提取 .zip 文件，并将文件夹**SimpleVMTemplate**的名称重命名为要在 Azure Stack 门户上显示的项的名称。

2. 创建 Azure 资源管理器模板或使用我们的 Windows/Linux 示例模板。 在步骤1中下载的包装器工具 .zip 文件中提供了这些示例模板。 您可以使用模板和更改文本字段，也可以从 GitHub 下载预配置的模板。 有关 Azure 资源管理器模板的详细信息，请参阅[azure 资源管理器模板](/azure/azure-resource-manager/resource-group-authoring-templates)。

3. 库包应包含以下结构：

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png" alt-text="库包":::

   部署模板文件结构如下所示：

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png" alt-text="库包":::

4. 将以下突出显示的值（包含数字的值）替换为在[上传自定义映像](azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal)时提供的值。

   > [!NOTE]  
   > 切勿对 Azure 资源管理器模板中的任何机密（例如产品密钥、密码或任何客户身份信息）进行硬编码。 将模板 JSON 文件发布到库中后，无法身份验证即可访问这些文件。 将所有机密存储在 [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) 中，然后从模板内部调用它们。

   以下模板是清单 json 文件的示例：

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "uiDefinition": {
          "path": "UIDefinition.json" (7)
          },
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "name": "<Template name>",
             "type": "Template",
             "path": "DeploymentTemplates\\<Template name>.json", (8)
             "isDefault": true
          }
       ],
       "categories":[ (9)
          "Custom",
          "<Template name>"
          ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (10)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    以下列表说明了示例模板中前面编号的值：

    - （1）-产品/服务的名称。
    - （2）–发布服务器的名称，不含空格。
    - （3）–模板的版本，不含空格。
    - （4）-客户看到的名称。
    - （5）-客户看到的发布者名称。
    - （6）-发布者合法名称。
    - （7）- **UIDefinition**文件的存储位置的路径。  
    - （8）-JSON 主模板文件的路径和名称。
    - （9）–显示此模板的类别的名称。
    - （10）–每个图标的路径和名称。

5. 对于引用**ms 资源**的所有字段，必须更改**字符串/资源 json**文件中的相应值：

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

    @no__t 0Package 显示 @ no__t-1 ![ 包显示 @ no__t

6. 若要确保资源可以成功部署，请用[Azure Stack api](../user/azure-stack-profiles-azure-resource-manager-versions.md)测试模板。

7. 如果你的模板依赖于虚拟机（VM）映像，请按照说明[将 VM 映像添加到 Azure Stack](azure-stack-add-vm-image.md)。

8. 将 Azure Resource Manager 模板保存在 **/Contoso.TodoList/DeploymentTemplates/** 文件夹中。

9. 为市场项选择图标和文本。 将图标添加到 **Icons** 文件夹，并向 **Strings** 文件夹中的 **resources** 文件添加文本。 为图标使用 **small**、**medium**、**large** 和 **wide** 命名约定。 有关这些大小的详细说明，请参阅[市场项 UI 参考](#reference-marketplace-item-ui)。

    > [!NOTE]
    > 为正确生成市场项，需要全部四个图标大小（small、medium、large、wide）。

10. 若要进一步编辑 .Manifest，请参阅 [Reference：市场项 manifest.json](#reference-marketplace-item-manifestjson)。

11. 完成修改文件后，请将其转换为 .azpkg 文件。 你可以使用**AzureGalleryPackager**工具和你先前下载的示例库包执行转换。 运行下面的命令：

    ```shell
    .\AzureGalleryPackager.exe package –m c:\<path>\<gallery package name>\manifest.json –o c:\Temp
    ```

    > [!NOTE]
    > 输出路径可以是你选择的任何路径，并且不必位于 C：驱动器下。 但是，必须存在到 .manifest 文件和输出包的完整路径。 例如，如果输出路径 `C:\<path>\galleryPackageName.azpkg`，则文件夹 @no__t 必须存在。
    >
    >

## <a name="publish-a-marketplace-item"></a>发布市场项目

1. 使用 PowerShell 或 Azure 存储资源管理器将市场项 (.azpkg) 上传到 Azure Blob 存储。 可以上传到本地 Azure Stack 存储或上传到 Azure 存储，这是包的临时位置。 请确保 blob 可公开访问。

2. 若要将库包导入 Azure Stack 中，第一步是将（RDP）远程连接到客户端 VM，以便将刚刚创建的文件复制到 Azure Stack。

3. 添加上下文：

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
    ```

4. 运行以下脚本，将资源导入到库中：

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg –Verbose
    ```

5. 验证你是否具有可用于存储项的有效存储帐户。 可以从 Azure Stack 管理员门户获取 @no__t 值0。 选择 "**存储帐户-> Blob 属性"-> URL**，扩展名为 .azpkg。 存储帐户仅供暂时使用，以便发布到 marketplace。

   完成库包并使用**AzsGalleryItem 将**其上传后，自定义 VM 现在应显示在 Marketplace 上，并显示在 "**创建资源**" 视图中。 请注意，自定义库包在**Marketplace 管理**中不可见。

   [![自定义 marketplace 项]已上传(media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "自定义 marketplace 项")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. 成功将项目发布到 marketplace 后，可以从存储帐户中删除内容。

   > [!CAUTION]  
   > 现在，无需身份验证，即可通过以下 URL 访问所有默认的库项目和自定义库项目：  
   `https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
   `https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`

6. 可以使用 **Remove-AzureRMGalleryItem** cmdlet 删除市场项。 例如：

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

   > [!NOTE]
   > 删除某个项后，市场 UI 可能会显示错误。 若要修复此错误，请在门户中单击“设置”。 然后，在“门户自定义”下选择“放弃修改”。
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>参考：市场项 manifest.json

### <a name="identity-information"></a>标识信息

| 名称 | 需要 | 类型 | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| 名称 |X |String |[A-Za-z0-9]+ | |
| 发布者 |X |String |[A-Za-z0-9]+ | |
| Version |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>元数据

| 名称 | 需要 | 类型 | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |建议不要超过 80 个字符 |如果项目名称的长度超过80个字符，门户可能不会正确显示您的项目名称。 |
| PublisherDisplayName |X |String |建议不要超过 30 个字符 |如果你的发布者名称长度超过30个字符，则它可能不会正确显示你的发布者名称。 |
| PublisherLegalName |X |String |最多 256 个字符 | |
| 总结 |X |String |60 到 100 个字符 | |
| LongSummary |X |String |140 到 256 个字符 |在 Azure Stack 中尚不适用。 |
| 描述 |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 到 5,000 个字符 | |

### <a name="images"></a>映像

市场使用以下图标：

| 名称 | 宽度 | 高度 | 说明 |
| --- | --- | --- | --- |
| 宽型 |255 px |115 px |始终必需 |
| 大型 |115 px |115 px |始终必需 |
| 中型 |90 px |90 px |始终必需 |
| 小 |40 px |40 px |始终必需 |
| 屏幕快照 |533 px |324 px |始终必需 |

### <a name="categories"></a>Categories

应当为每个市场项标记一个类别，该类别标识在门户 UI 中的何处显示该项。 可以在 Azure Stack 中选择现有类别之一（"**计算**"、"**数据 + 存储**" 等），或选择一个新类别。

### <a name="links"></a>链接

每个市场项可以包括指向其他内容的各种链接。 链接以名称和 URI 的列表形式进行指定：

| 名称 | 需要 | 类型 | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 64 个字符。 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>其他属性

除了前面的元数据之外，市场作者可以采用以下形式提供自定义键/值对数据：

| 名称 | 需要 | type | 约束 | 描述 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 25 个字符。 | |
| ReplTest1 |X |String |最多 30 个字符。 | |

### <a name="html-sanitization"></a>HTML 清理

对于允许使用 HTML 的任何字段，将[允许使用以下元素和属性](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization)：

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>参考：市场项 UI

在 Azure Stack 门户中显示的市场项的图标和文本将如下所示。

### <a name="create-blade"></a>“创建”边栏选项卡

![创建边栏选项卡-Azure Stack Marketplace 项](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>市场项详细信息边栏选项卡

![Azure Stack Marketplace 项详细信息边栏选项卡](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>后续步骤

- [Azure Stack 市场概述](azure-stack-marketplace.md)
- [下载市场项](azure-stack-download-azure-marketplace-item.md)
- [Azure 资源管理器模板的格式和结构](/azure/azure-resource-manager/resource-group-authoring-templates)
