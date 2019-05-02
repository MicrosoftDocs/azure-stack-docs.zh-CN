---
title: 工作流通用参数中作为服务的 Azure Stack 验证 |Microsoft Docs
description: 工作流通用参数作为服务的 Azure Stack 验证
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0979588e358d1e163a29ab46bdbe99c27fc37649
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299830"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>工作流通用参数作为服务的 Azure Stack 验证

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

常见参数包括值，如环境变量和用户凭据所需的验证作为一项服务 (VaaS) 中的所有测试。 创建或更改工作流时在工作流级别定义这些值。 当计划的测试，这些值是作为参数传递到工作流在每个测试。

> [!NOTE]
> 每个测试定义自己的参数集。 在计划时，测试可能要求您输入的值独立于通用参数，或可能会允许你重写公共参数值。

## <a name="environment-parameters"></a>环境参数

环境参数描述待测试的 Azure Stack 环境。 通过生成并上传要测试的特定实例的 Azure Stack 戳信息文件，必须提供这些值。

> [!NOTE]
> 在正式验证工作流中创建工作流后无法修改环境的参数。

### <a name="generate-the-stamp-information-file"></a>生成的标记信息文件

1. 登录到此 DVM 或有权访问 Azure Stack 环境的任何计算机。
2. 在提升的 PowerShell 窗口中运行以下命令：

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>查找 ECE 配置文件中的值

环境参数值还可以手动位于中**ECE 配置文件**位于`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`DVM 上。

## <a name="test-parameters"></a>测试参数

常见的测试参数包括不能在配置文件中存储的敏感信息。 这些必须手动提供。

参数    | 描述
-------------|-----------------
租户管理员用户                            | Azure Active Directory 租户管理员预配的 AAD 目录中的服务管理员。 此用户执行租户级的操作，例如部署模板来设置资源 (Vm、 存储帐户，等等) 和执行工作负荷。 有关预配的租户帐户的详细信息，请参阅[添加新的 Azure Stack 租户](../operator/azure-stack-add-new-user-aad.md)。
服务管理员用户             | 在 Azure Stack 部署过程中指定的 AAD Directory 租户的 azure Active Directory 管理员。 搜索`AADTenant`中的 ECE 配置文件，然后选择中的值`UniqueName`元素。
云管理员用户               | Azure Stack 域管理员帐户 (例如， `contoso\cloudadmin`)。 搜索`User Role="CloudAdmin"`中的 ECE 配置文件，然后选择中的值`UserName`元素。
诊断连接字符串          | 指向 Azure 存储帐户的诊断日志将复制到测试执行期间的 SAS URL。 有关生成 SAS URL 的说明，请参阅[生成的诊断连接字符串](#generate-the-diagnostics-connection-string)。 |

> [!IMPORTANT]
> **诊断连接字符串**必须是有效的继续操作之前。

### <a name="generate-the-diagnostics-connection-string"></a>生成的诊断连接字符串

需要在测试执行过程中存储诊断日志的诊断连接字符串。 使用安装过程中创建的 Azure 存储帐户 (请参阅[设置为服务资源将验证](azure-stack-vaas-set-up-resources.md)) 若要创建的共享的访问签名 (SAS) URL，以便将日志上传到存储帐户的 VaaS 访问权限。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 选择**Blob**从**允许的服务选项**。 取消选择任何剩余的选项。

1. 选择**服务**，**容器**，并**对象**从**允许的资源类型**。

1. 选择**读**，**编写**，**列表**，**添加**，**创建**从**允许权限**。 取消选择任何剩余的选项。

1. 将“开始时间”设置为当前时间，将“结束时间”设置为距当前时间三个月的未来时间。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> SAS URL 将在生成 URL 时指定的结束时间到期。  
当计划的测试，请确保至少 30 天，该 URL 有效，以及用于 （建议三个月内） 执行测试所需的时间。

## <a name="next-steps"></a>后续步骤

- 了解有关[作为服务的关键概念验证](azure-stack-vaas-key-concepts.md)