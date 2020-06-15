---
title: 在 Azure Stack Hub 中创建和发布市场项
description: 了解如何创建并发布 Azure Stack Hub 市场项。
author: sethmanheim
ms.topic: article
ms.date: 06/11/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 195b6ef3b15ada39664871557ed7efe2d613f5cd
ms.sourcegitcommit: dd140b3a2ac8e558eae9f5f422711d2ba560da16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744940"
---
# <a name="create-and-publish-a-custom-azure-stack-hub-marketplace-item"></a>创建并发布自定义 Azure Stack Hub 市场项

发布到 Azure Stack Hub 市场的每个项都使用 Azure 库包 (.azpkg) 格式。 使用 *Azure Gallery Packager* 工具可以创建自定义 Azure 库包，然后可将此包上传到 Azure Stack Hub 市场供用户下载。 部署过程使用 Azure 资源管理器模板。

## <a name="marketplace-items"></a>市场项

本文中的示例演示如何创建 Windows 或 Linux 类型的单个 VM 市场套餐。

## <a name="create-a-marketplace-item"></a>创建市场项

> [!IMPORTANT]
> 在创建 VM 市场项之前，请将自定义 VM 映像上传到 Azure Stack Hub 门户，并根据[将 VM 映像添加到 Azure Stack Hub](azure-stack-add-vm-image.md) 中的说明操作。 然后，根据本文中的说明打包映像（创建 .azpkg），并将其上传到 Azure Stack Hub 市场。

若要创建自定义市场项，请执行以下操作：

1. 下载 [Azure Gallery Packager 工具](https://aka.ms/azsmarketplaceitem)和示例 Azure Stack Hub 库包。 下载内容包括自定义的 VM 模板。 解压缩 .zip 文件，在文件夹 **Custom VMs** 下，可以使用适用的 Linux 或 Windows 模板。 可以决定重复使用预制的模板，并使用要显示在 Azure Stack Hub 门户中的项的产品详细信息修改相应的参数。 或者，可以只重复使用可用的 .azpkg 文件，并跳过后续的自定义自己库包的步骤。

2. 创建 Azure 资源管理器模板，或使用适用于 Windows/Linux 的示例模板。 在步骤 1 下载的打包器工具 .zip 文件中已提供这些示例模板。 可以使用模板并更改文本字段，或者从 GitHub 下载预配置的模板。 有关 Azure 资源管理器模板的详细信息，请参阅 [Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-authoring-templates)。

3. 库包应包含以下结构：

   ![库包结构的屏幕截图](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

   部署模板文件结构如下所示：

   ![部署模板结构的屏幕截图](media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png)

4. 将 Manifest.json 模板中突出显示的以下值（带编号的值）替换为在[上传自定义映像](azure-stack-add-vm-image.md)时提供的值。

   > [!NOTE]  
   > 切勿对 Azure 资源管理器模板中的任何机密（例如产品密钥、密码或任何客户身份信息）进行硬编码。 将模板 JSON 文件发布到库中后，无法身份验证即可访问这些文件。 将所有机密存储在 [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) 中，然后从模板内部调用它们。

   以下模板是 Manifest.json 文件的示例：

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

    以下列表解释了示例模板中的上述带有编号的值：

    - （1）-产品/服务的名称。
    - （2）–发布服务器的名称，不含空格。
    - （3）–模板的版本，不含空格。
    - （4）-客户看到的名称。
    - （5）-客户看到的发布者名称。
    - （6）-发布者合法名称。
    - （7）-存储**UIDefinition.js**文件的路径。  
    - （8）-JSON 主模板文件的路径和名称。
    - （9）–显示此模板的类别的名称。
    - （10）–每个图标的路径和名称。

5. 对于引用 **ms-resource** 的所有字段，必须在 **strings/resources.json** 文件中更改相应的值：

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

    ![包显示](media/azure-stack-create-and-publish-marketplace-item/pkg1.png) ![包显示](media/azure-stack-create-and-publish-marketplace-item/pkg2.png)

6. 为确保资源可以成功部署，请使用 [Azure Stack Hub API](../user/azure-stack-profiles-azure-resource-manager-versions.md) 测试该模板。

7. 如果你的模板依赖于虚拟机 (VM) 映像，请根据说明[将 VM 映像添加到 Azure Stack Hub](azure-stack-add-vm-image.md)。

8. 将 Azure Resource Manager 模板保存在 **/Contoso.TodoList/DeploymentTemplates/** 文件夹中。

9. 为市场项选择图标和文本。 将图标添加到 **Icons** 文件夹，并向 **Strings** 文件夹中的 **resources** 文件添加文本。 为图标使用 **small**、**medium**、**large** 和 **wide** 命名约定。 有关这些大小的详细说明，请参阅[市场项 UI 参考](#reference-marketplace-item-ui)。

    > [!NOTE]
    > 为正确生成市场项，需要全部四个图标大小（small、medium、large、wide）。

10. 若要进一步编辑 Manifest.json，请参阅[参考：市场项 manifest.json](#reference-marketplace-item-manifestjson)。

11. 修改完文件后，请将其转换为 .azpkg 文件。 可以使用 **AzureGallery.exe** 工具以及前面下载的示例库包来执行转换。 运行以下命令：

    ```shell
    .\AzureGallery.exe package –m c:\<path>\<gallery package name>\manifest.json –o c:\Temp
    ```

    > [!NOTE]
    > 输出路径可以是所选的任何路径，且不一定要在 C: 驱动器下。 但是，manifest.json 文件和输出包的完整路径必须存在。 例如，如果输出路径为 `C:\<path>\galleryPackageName.azpkg`，则文件夹 `C:\<path>` 必须存在。
    >
    >

## <a name="publish-a-marketplace-item"></a>发布市场项

1. 使用 PowerShell 或 Azure 存储资源管理器将市场项 (.azpkg) 上传到 Azure Blob 存储。 可以上传到本地 Azure Stack Hub 存储或上传到 Azure 存储，即包的临时位置。 请确保 blob 可公开访问。

2. 若要将库包导入 Azure Stack Hub 中，首先请远程连接 (RDP) 到客户端 VM，以便将刚刚创建的文件复制到 Azure Stack Hub。

3. 添加上下文：

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
    ```

4. 运行以下脚本，将资源导入库中：

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg –Verbose
    ```

5. 确认是否可以提供一个有效的存储帐户来存储项。 可以从 Azure Stack Hub 管理员门户获取 `GalleryItemURI` 值。 选择“存储帐户”>“Blob 属性”->“URL”，扩展名为 .azpkg。  存储帐户仅供暂时使用，以便能够发布到市场。

   完成库包并使用 **Add-AzsGalleryItem** 将其上传之后，自定义 VM 现在应会显示在市场中以及“创建资源”视图中。  请注意，“市场管理”中不显示自定义库包。 

   [![已上传自定义市场项](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "已上传自定义市场项")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. 成功将项发布到市场后，可以删除存储帐户中的内容。

   > [!CAUTION]  
   > 现在，无需身份验证，即可通过以下 URL 访问所有默认的库项目和自定义库项目：  
   `https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
   `https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`

6. 可以使用 **Remove-AzureRMGalleryItem** cmdlet 删除市场项。 例如：

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

   > [!NOTE]
   > 删除某个项后，市场 UI 可能会显示错误。 若要修复此错误，请在门户中单击“设置”  。 然后，在“门户自定义”下选择“放弃修改”。  
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>参考：市场项 manifest.json

### <a name="identity-information"></a>标识信息

| 名称 | 必须 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| 名称 |X |String |[A-Za-z0-9]+ | |
| 发布者 |X |String |[A-Za-z0-9]+ | |
| 版本 |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadata

| 名称 | 必须 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |建议不要超过 80 个字符 |如果长度超过 80 个字符，门户可能无法正确地显示项名称。 |
| PublisherDisplayName |X |String |建议不要超过 30 个字符 |如果长度超过 30 个字符，门户可能无法正确地显示发布者名称。 |
| PublisherLegalName |X |String |最多 256 个字符 | |
| 摘要 |X |String |60 到 100 个字符 | |
| LongSummary |X |String |140 到 256 个字符 |在 Azure Stack Hub 中尚不适用。 |
| 说明 |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 到 5,000 个字符 | |

### <a name="images"></a>映像

市场使用以下图标：

| 名称 | 宽度 | 高度 | 注释 |
| --- | --- | --- | --- |
| Wide |255 px |115 px |始终必需 |
| 大型 |115 px |115 px |始终必需 |
| 中型 |90 px |90 px |始终必需 |
| 小型 |40 px |40 px |始终必需 |
| 屏幕快照 |533 px |324 px |可选 |

### <a name="categories"></a>Categories

应当为每个市场项标记一个类别，该类别标识在门户 UI 中的何处显示该项。 可以选择 Azure Stack Hub 中的现有类别之一（“计算”、“数据 + 存储”等），也可以选择新建一个。  

### <a name="links"></a>链接

每个市场项可以包括指向其他内容的各种链接。 链接以名称和 URI 的列表形式进行指定：

| 名称 | 必须 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 64 个字符。 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>其他属性

除了前面的元数据之外，市场作者可以采用以下形式提供自定义键/值对数据：

| 名称 | 必须 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 25 个字符。 | |
| Value |X |String |最多 30 个字符。 | |

### <a name="html-sanitization"></a>HTML 清理

对于允许使用 HTML 的任何字段，将[允许使用以下元素和属性](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization)：

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>参考：市场项 UI

在 Azure Stack Hub 门户中显示的市场项的图标和文本将如下所示。

### <a name="create-blade"></a>“创建”边栏选项卡

![创建边栏选项卡-Azure Stack 集线器 Marketplace 项](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>市场项详细信息边栏选项卡

![Azure Stack Hub 市场项详细信息边栏选项卡](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub 市场概述](azure-stack-marketplace.md)
- [下载市场项](azure-stack-download-azure-marketplace-item.md)
- [Azure 资源管理器模板的格式和结构](/azure/azure-resource-manager/resource-group-authoring-templates)
