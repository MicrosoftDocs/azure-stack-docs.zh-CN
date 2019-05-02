---
title: 验证 Azure Stack 验证作为一项服务中的原始设备制造商 (OEM) 包 |Microsoft Docs
description: 了解如何进行验证的验证作为一项服务的原始设备制造商 (OEM) 包。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8dca51f274d5bcb5e761f701963ed4bd62889b3f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297784"
---
# <a name="validate-oem-packages"></a>验证 OEM 程序包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

当对已完成的解决方案验证的固件或驱动程序进行了更改时，可以测试新的 OEM 程序包。 在您的软件包经过测试，它是由 Microsoft 签名。 你的测试必须包含更新后的 OEM 扩展程序包，其中包含已通过 Windows Server 徽标和 PCS 测试的驱动程序和固件。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 在上载或提交包之前, 查看[创建一个 OEM 包](azure-stack-vaas-create-oem-package.md)预期的包格式和内容。

## <a name="managing-packages-for-validation"></a>管理包进行验证

使用时**包验证**工作流，以验证包中，将需要提供的 URL **Azure 存储 blob**。 此 blob 是测试签名将作为更新过程的一部分安装的 OEM 包。 创建使用在安装期间创建的 Azure 存储帐户的 blob (请参阅[设置为服务资源将验证](azure-stack-vaas-set-up-resources.md))。

### <a name="prerequisite-provision-a-storage-container"></a>先决条件：预配存储容器

包的 blob 在存储帐户中创建容器。 此容器可以用于所有包验证运行。

1. 在中[Azure 门户](https://portal.azure.com)，请转到在中创建的存储帐户[设置为服务资源将验证](azure-stack-vaas-set-up-resources.md)。

2. 在左侧边栏选项卡上**Blob 服务**，选择**容器**。

3. 选择 **+ 容器**从菜单栏中。
    1. 为容器提供名称，例如， `vaaspackages`。
    1. 为未经身份验证的客户端，例如 VaaS 选择所需的访问级别。 有关如何授予对每个方案中的包的 VaaS 访问的详细信息，请参阅[处理容器的访问级别](#handling-container-access-level)。

### <a name="upload-package-to-storage-account"></a>将包上载到存储帐户

1. 准备要验证的包。 这是`.zip`文件的内容必须与匹配的结构中所述[创建一个 OEM 包](azure-stack-vaas-create-oem-package.md)。

    > [!NOTE]
    > 请确保`.zip`内容的根目录处放置`.zip`文件。 包应为任何子文件夹。

1. 在中[Azure 门户](https://portal.azure.com)，选择包容器并上传上选择包**上传**菜单栏中。

1. 选择的包`.zip`要上传文件。 保留默认值为**Blob 类型**(即**块 Blob**) 和**块大小**。

### <a name="generate-package-blob-url-for-vaas"></a>为 VaaS 生成包 blob URL

创建时**包验证**VaaS 门户中的工作流，你将需要提供包含包的 Azure 存储 blob 的 URL。 某些*交互式*测试，包括**每月 AzureStack 更新验证**并**OEM 扩展包验证**，还需要指向包的 blob 的 URL。

#### <a name="handling-container-access-level"></a>处理容器的访问级别

VaaS 所需的最低访问级别取决于是否创建包验证工作流或调度*交互式*测试。

情况下**私有**并**Blob**访问级别，您暂时必须授予对包 blob 的访问，从而 VaaS[共享的访问签名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?)(SAS)。 **容器**访问级别不需要，可以生成 SAS Url，但允许对容器及其 blob 的未经身份验证的访问。

|访问级别 | 工作流要求 | 测试要求 |
|---|---------|---------|
|专用 | 生成每个包 blob 的 SAS URL ([选项 1](#option-1-generate-a-blob-sas-url))。 | 生成在帐户级别 SAS URL 并手动添加包 blob 名称 ([选项 2](#option-2-construct-a-container-sas-url))。 |
|Blob | 提供 blob URL 属性 ([选项 3](#option-3-grant-public-read-access))。 | 生成在帐户级别 SAS URL 并手动添加包 blob 名称 ([选项 2](#option-2-construct-a-container-sas-url))。 |
|容器 | 提供 blob URL 属性 ([选项 3](#option-3-grant-public-read-access))。 | 提供 blob URL 属性 ([选项 3](#option-3-grant-public-read-access))。

授予对您的包访问权限的选项按从最低访问到最大访问的顺序排列。

#### <a name="option-1-generate-a-blob-sas-url"></a>选项 1：生成 blob SAS URL

如果您的存储容器的访问级别设置为使用此选项**专用**、 容器不会启用对容器或其 blob 的公共读取访问权限。

> [!NOTE]
> 此方法不适用于*交互式*测试。 请参阅[选项 2:构造容器 SAS URL](#option-2-construct-a-container-sas-url)。

1. 在中[Azure 门户](https://portal.azure.com/)，请转到你的存储帐户，然后导航到包含您的包.zip。

2. 选择**生成 SAS**从上下文菜单。

3. 选择**读**从**权限**。

4. 设置**开始时间**为当前时间，并**结束时间**到 48 小时至少从**开始时间**。 如果要创建其他工作流与同一个包，请考虑增大**结束时间**测试的长度。

5. 选择“生成 Blob SAS 令牌和 URL”。

使用**Blob SAS URL**时提供包 blob 到门户的 Url。

#### <a name="option-2-construct-a-container-sas-url"></a>选项 2：构造容器 SAS URL

如果您的存储容器的访问级别设置为使用此选项**私有**并且需要提供给包的 blob URL*交互式*测试。 此外可以在工作流级别使用此 URL。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 选择**Blob**从**允许的服务选项**。 取消选择任何剩余的选项。

1. 选择**容器**并**对象**从**允许的资源类型**。

1. 选择**读**并**列表**从**允许权限**。 取消选择任何剩余的选项。

1. 选择**开始时间**作为当前时间和**结束时间**到 14 天至少从**开始时间**。 如果你将在其他测试运行使用同一个包，请考虑增大**结束时间**测试的长度。 通过 VaaS 后计划的任何测试**结束时间**将失败，而且新的 SAS 将需要生成。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    格式应如下所示： `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. 修改生成的 SAS URL，包括包容器`{containername}`，和包 blob 的名称`{mypackage.zip}`，按如下所示：  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    提供包 blob Url 复制到门户时，请使用此值。

#### <a name="option-3-grant-public-read-access"></a>选项 3：授予公共读取访问权限

使用此选项，如果允许未经身份验证的客户端访问各个 blob，或的情况下，可以接受*交互式*测试容器。

> [!CAUTION]
> 此选项将打开你个 blob 的匿名的只读访问权限。

1. 设置到包容器的访问级别**Blob**或**容器**按照以下部分中的说明[授予匿名用户权限对容器和 blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > 如果你要提供的包 URL*交互式*测试，必须授予**完全公共读取访问**到容器，以继续进行测试。

1. 在包容器中，选择要打开属性窗格中的包 blob。

1. 复制**URL**。 提供包 blob Url 复制到门户时，请使用此值。

## <a name="create-a-package-validation-workflow"></a>创建包验证工作流

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 选择**启动**上**包验证**磁贴。

    ![包验证工作流磁贴](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 输入 Azure 存储 blob URL 测试签名 OEM 包需要来自 Microsoft 的签名。 有关说明，请参阅[VaaS 生成包 blob URL](#generate-package-blob-url-for-vaas)。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 创建工作流后，无法修改环境的参数。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    你将重定向到测试摘要页。

## <a name="required-tests"></a>所需的测试

以下测试所需的 OEM 包验证：

- OEM 扩展包验证
- 云模拟引擎

## <a name="run-package-validation-tests"></a>运行包验证测试

1. 在中**包验证测试摘要**页上，将运行的适用于你的方案列出测试的子集。

    验证工作流中**安排**测试使用工作流创建期间指定的工作流级别公用参数 (请参阅[工作流通用参数作为服务AzureStack验证](azure-stack-vaas-parameters.md)). 如果任何测试参数值变得无效，您必须 resupply 它们中所述[修改工作流参数](azure-stack-vaas-monitor-test.md#change-workflow-parameters)。

    > [!NOTE]
    > 计划高于现有实例的一个验证测试，将在门户中创建的新实例来代替旧实例。 旧实例的日志将保留，但不能从门户访问。  
    > 测试成功，完成后**计划**操作将被禁用。

2. 选择将运行测试的代理。 有关添加信息本地测试执行代理，请参阅[部署本地代理](azure-stack-vaas-local-agent.md)。

3. 若要完成 OEM 扩展包验证，请选择**计划**从上下文菜单打开的提示的计划的测试实例。

4. 查看测试参数，然后选择**提交**要安排执行 OEM 扩展包验证。

    OEM 扩展包验证拆分为两个手动步骤：Azure Stack 更新和 OEM 更新。

   1. **选择**"运行"用户界面来执行预检查脚本中。 这是自动的测试，需要大约 5 分钟才能完成，并且不需要执行任何操作。

   1. 预检查脚本完成后，请执行手动步骤：**安装**的最新可用 Azure Stack 更新使用 Azure Stack 门户。

   1. **运行**Test-azurestack 在标记上。 如果发生任何故障，不会继续使用测试和联系人[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)。

       有关如何运行 Test-azurestack 命令的信息，请参阅[验证 Azure Stack 系统状态](../operator/azure-stack-diagnostic-test.md)。

   1. **选择**"下一步"以执行 postcheck 脚本。 这是自动的测试，表示 Azure Stack 更新过程的结尾。

   1. **选择**"运行"OEM 更新执行预检查的脚本。

   1. 完成预检查后，执行手动步骤：**安装**通过门户将 OEM 扩展包。

   1. **运行**Test-azurestack 在标记上。

      > [!NOTE]
      > 与前面一样，不会继续使用测试和联系人[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)失败。 此步骤非常重要，因为它可以节省重新部署。

   1. **选择**"下一步"以执行 postcheck 脚本。 这会标记 OEM 更新步骤结束时。

   1. 回答测试结束时的任何剩余问题并**选择**"提交"。

   1. 这表示交互式测试的结尾。

5. 查看 OEM 扩展包验证结果。 成功测试之后，计划云模拟引擎执行。

若要提交申请进行签名的包，将发送[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)的解决方案名称和包验证名称与此运行相关联。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
