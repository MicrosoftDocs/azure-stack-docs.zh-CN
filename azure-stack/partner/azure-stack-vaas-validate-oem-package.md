---
title: 验证作为服务 Azure Stack 验证中的原始设备制造商 (OEM) 包 |Microsoft Docs
description: 了解如何使用验证即服务验证原始设备制造商 (OEM) 包。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 69bb9c89793789280debe13a142f4c96470f7c31
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418312"
---
# <a name="validate-oem-packages"></a>验证 OEM 程序包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

当对已完成的解决方案验证的固件或驱动程序进行了更改时，可以测试新的 OEM 程序包。 当你的包已通过测试时, 它由 Microsoft 签名。 你的测试必须包含更新后的 OEM 扩展程序包，其中包含已通过 Windows Server 徽标和 PCS 测试的驱动程序和固件。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 在上传或提交包之前, 请查看创建适用于预期包格式和内容的[OEM 包](azure-stack-vaas-create-oem-package.md)。

## <a name="managing-packages-for-validation"></a>管理用于验证的包

使用**包验证**工作流验证包时, 需要提供**Azure 存储 blob**的 URL。 此 blob 是测试签名的 OEM 包, 将作为更新过程的一部分进行安装。 使用在安装过程中创建的 Azure 存储帐户 (请参阅[将验证设置为服务资源](azure-stack-vaas-set-up-resources.md)) 创建 blob。

### <a name="prerequisite-provision-a-storage-container"></a>先决条件：预配存储容器

在存储帐户中为包 blob 创建容器。 此容器可用于所有包验证运行。

1. 在[Azure 门户](https://portal.azure.com)中, 请参阅[将验证设置为服务资源](azure-stack-vaas-set-up-resources.md)中创建的存储帐户。

2. 在 " **Blob 服务**" 下的左侧边栏选项卡中, 选择 "**容器**"。

3. 从菜单栏中选择 " **+ 容器**"。
    1. 为容器提供一个名称, 例如`vaaspackages`。
    1. 为未经身份验证的客户端 (如 VaaS) 选择所需的访问级别。 有关如何在每个方案中授予对包的 VaaS 访问权限的详细信息, 请参阅[处理容器访问级别](#handling-container-access-level)。

### <a name="upload-package-to-storage-account"></a>将包上传到存储帐户

1. 准备要验证的包。 这是一个`.zip`文件, 其内容必须与[创建 OEM 包](azure-stack-vaas-create-oem-package.md)中所述的结构相匹配。

    > [!NOTE]
    > 请确保`.zip`将内容放在`.zip`文件的根目录下。 包中不应有子文件夹。

1. 在[Azure 门户](https://portal.azure.com)中, 选择 "包" 容器, 然后在菜单栏中选择 "上**传**" 上传包。

1. 选择要上`.zip`传的包文件。 保留**blob 类型**的默认值 (即**块 Blob**) 和**块大小**。

### <a name="generate-package-blob-url-for-vaas"></a>为 VaaS 生成包 blob URL

在 VaaS 门户中创建**包验证**工作流时, 需要提供包含包的 Azure 存储 BLOB 的 URL。 某些*交互式*测试 (包括**每月 test-azurestack 更新验证**和**OEM 扩展包验证**) 还需要包 blob 的 URL。

#### <a name="handling-container-access-level"></a>处理容器访问级别

VaaS 所需的最低访问级别取决于你是要创建包验证工作流还是计划*交互*测试。

如果是**私有**和**Blob**访问级别, 则必须通过向 VaaS 提供[共享访问签名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?)(SAS) 来暂时授予对包 Blob 的访问权限。 **容器**访问级别不要求你生成 SAS url, 但允许对容器及其 blob 进行未经身份验证的访问。

|访问级别 | 工作流要求 | 测试要求 |
|---|---------|---------|
|专用 | 生成每个包 blob 的 SAS URL ([选项 1](#option-1-generate-a-blob-sas-url))。 | 在帐户级别生成 SAS URL, 并手动添加包 blob 名称 ([选项 2](#option-2-construct-a-container-sas-url))。 |
|Blob | 提供 "blob URL" 属性 ([选项 3](#option-3-grant-public-read-access))。 | 在帐户级别生成 SAS URL, 并手动添加包 blob 名称 ([选项 2](#option-2-construct-a-container-sas-url))。 |
|容器 | 提供 "blob URL" 属性 ([选项 3](#option-3-grant-public-read-access))。 | 提供 "blob URL" 属性 ([选项 3](#option-3-grant-public-read-access))。

用于授予对包的访问权限的选项按最小访问权限的顺序排列。

#### <a name="option-1-generate-a-blob-sas-url"></a>选项 1：生成 blob SAS URL

如果存储容器的访问级别设置为 "**专用**", 则使用此选项, 容器不会启用对容器或其 blob 的公共读取访问权限。

> [!NOTE]
> 此方法对于*交互式*测试不起作用。 请[参阅选项 2:构造容器 SAS URL](#option-2-construct-a-container-sas-url)。

1. 在[Azure 门户](https://portal.azure.com/)中, 转到你的存储帐户, 然后导航到包含包的 .zip。

2. 从上下文菜单中选择 "**生成 SAS** "。

3. 选择 "从**权限** **读取**"。

4. 将**开始时间**设置为当前时间, 并将**结束时间**设置为从**开始时间起**至少48小时。 如果将创建具有相同包的其他工作流, 请考虑增加测试长度的**结束时间**。

5. 选择“生成 Blob SAS 令牌和 URL”。

将包 Blob url 提供给门户时, 请使用**BLOB SAS url** 。

#### <a name="option-2-construct-a-container-sas-url"></a>选项 2：构造容器 SAS URL

如果存储容器的访问级别设置为 "**专用**", 并且需要提供*交互式*测试的包 blob URL, 请使用此选项。 此 URL 还可在工作流级别使用。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 从 "**允许的服务" 选项**中选择 " **Blob** "。 取消选择任何其他选项。

1. 从**允许的资源类型**中选择**容器**和**对象**。

1. 从**允许的权限**中选择 "**读取**" 和 "**列表**"。 取消选择任何其他选项。

1. 从**开始**时间开始, 选择 "**开始时间**" 作为 "当前时间" 和 "**结束时间**"。 如果将运行具有相同包的其他测试, 请考虑增加测试长度的**结束时间**。 **结束时间**之后通过 VaaS 计划的所有测试都将失败, 并将需要生成新的 SAS。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    格式应如下所示:`https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. 修改生成的 SAS URL, 使其包括包的`{containername}`容器、和包`{mypackage.zip}`blob 的名称, 如下所示:`https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    将包 blob Url 提供给门户时, 请使用此值。

#### <a name="option-3-grant-public-read-access"></a>选项 3：授予公共读取访问权限

如果允许未经身份验证的客户端访问单独的 blob, 则使用此选项; 对于*交互式*测试, 则使用此选项。

> [!CAUTION]
> 此选项将打开你的 blob 进行匿名只读访问。

1. 按照[为容器和 Blob 授予匿名用户权限](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)一节中的说明, 将包容器的访问级别设置为**Blob**或**容器**。

    > [!NOTE]
    > 如果要向*交互式*测试提供包 URL, 则必须授予对容器的**完全公共读取访问权限**, 以继续进行测试。

1. 在 "包" 容器中, 选择要打开 "属性" 窗格的包 blob。

1. 复制**URL**。 将包 blob Url 提供给门户时, 请使用此值。

## <a name="create-a-package-validation-workflow"></a>创建包验证工作流

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 选择 "**包验证**" 磁贴上的 "**开始**"。

    ![包验证工作流磁贴](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 将 Azure 存储 blob URL 输入到需要 Microsoft 签名的测试签名 OEM 包。 有关说明, 请参阅为[VaaS 生成包 BLOB URL](#generate-package-blob-url-for-vaas)。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 创建工作流后, 不能修改环境参数。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    你将被重定向到 "测试摘要" 页。

## <a name="required-tests"></a>必需的测试

OEM 包验证需要以下测试:

- OEM 扩展包验证
- 云模拟引擎

## <a name="run-package-validation-tests"></a>运行包验证测试

1. 在 "**包验证测试摘要**" 页中, 你将运行适用于你的方案的所列测试的子集。

    在验证工作流中,**计划**测试使用工作流创建期间指定的工作流级公共参数 ([有关 Azure Stack 验证作为服务的工作流通用参数](azure-stack-vaas-parameters.md), 请参阅)。 如果任何测试参数值无效, 则必须按照[修改工作流参数](azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示 resupply 它们。

    > [!NOTE]
    > 在现有实例上计划验证测试会在门户中创建一个新实例来代替旧实例。 旧实例的日志将保留, 但无法从门户进行访问。  
    > 测试成功完成后,**计划**操作将变为禁用状态。

2. 选择要运行测试的代理。 有关添加本地测试执行代理的信息, 请参阅[部署本地代理](azure-stack-vaas-local-agent.md)。

3. 若要完成 OEM 扩展包验证, 请从上下文菜单中选择 "**计划**" 以打开计划测试实例的提示。

4. 查看测试参数, 然后选择 "**提交**" 以计划要执行的 OEM 扩展包验证。

    OEM 扩展包验证分为两个手动步骤:Azure Stack 更新和 OEM 更新。

   1. **选择**UI 中的 "运行" 以执行预检查脚本。 这是一种自动测试, 需要大约5分钟的时间才能完成, 无需采取任何措施。

   1. 完成预检查脚本后, 请执行手动步骤: 使用 Azure Stack 门户**安装**最新的可用 Azure Stack 更新。

   1. **运行**戳记上的 Test-azurestack。 如果出现任何故障, 请不要继续测试并联系[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)。

       有关如何运行 Test-azurestack 命令的信息, 请参阅[验证 Azure Stack 系统状态](../operator/azure-stack-diagnostic-test.md)。

   1. **选择**"下一步", 执行 postcheck 脚本。 这是一种自动测试, 用于标记 Azure Stack 更新过程的结束。

   1. **选择**"运行", 为 OEM 更新执行预检查脚本。

   1. 完成预检查后, 请执行手动步骤: 通过门户**安装**OEM 扩展包。

   1. **运行**戳记上的 Test-azurestack。

      > [!NOTE]
      > 与前面一样, 如果测试失败, 请不要继续[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)测试并联系。 此步骤至关重要, 因为它将节省重新部署。

   1. **选择**"下一步", 执行 postcheck 脚本。 这会标记 OEM 更新步骤的结束。

   1. 在测试结束时回答所有剩余问题, 然后**选择**"提交"。

   1. 这会标记交互式测试的结尾。

5. 查看 OEM 扩展包验证的结果。 测试成功后, 计划云模拟引擎执行。

若要提交包签名请求, 请[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)发送与此运行关联的解决方案名称和包验证名称。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
