---
title: Azure Stack 验证中的工作流通用参数作为服务 |Microsoft Docs
description: 作为服务 Azure Stack 验证的工作流常见参数
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e5467945db4987bdd86b7265117c486fb6f7e1b
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418341"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>作为服务 Azure Stack 验证的工作流常见参数

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

常见参数包括环境变量等值和验证即服务中的所有测试所需的用户凭据 (VaaS)。 这些值是在创建或更改工作流时在工作流级别定义的。 计划测试时, 这些值作为参数传递给工作流下的每个测试。

> [!NOTE]
> 每个测试都定义了其自己的一组参数。 在计划时间, 测试可能要求你输入独立于通用参数的值, 或可能允许你重写通用参数值。

## <a name="environment-parameters"></a>环境参数

环境参数描述待测试的 Azure Stack 环境。 必须通过为要测试的特定实例生成和上传 Azure Stack 戳记信息文件来提供这些值。

> [!NOTE]
> 在官方验证工作流中, 在创建工作流后, 不能修改环境参数。

### <a name="generate-the-stamp-information-file"></a>生成戳记信息文件

1. 登录到 DVM 或有权访问 Azure Stack 环境的任何计算机。
2. 在提升的 PowerShell 窗口中运行以下命令:

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>在 ECE 配置文件中查找值

还可以在位于`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` DVM 上的**ECE 配置文件**中手动找到环境参数值。

## <a name="test-parameters"></a>测试参数

常见测试参数包括无法存储在配置文件中的敏感信息。 必须手动提供这些。

参数    | 描述
-------------|-----------------
租户管理员用户                            | Azure Active Directory 在 AAD 目录中由服务管理员预配租户管理员。 此用户执行租户级别的操作, 例如部署模板来设置资源 (Vm、存储帐户等) 和执行工作负荷。 有关设置租户帐户的详细信息, 请参阅[添加新的 Azure Stack 租户](../operator/azure-stack-add-new-user-aad.md)。
服务管理员用户             | Azure Active Directory 在 Azure Stack 部署过程中指定的 AAD 目录租户的管理员。 在 ECE 配置文件中搜索, 然后在`UniqueName`元素中选择值。 `AADTenant`
云管理员用户               | Azure Stack 域管理员帐户 (例如`contoso\cloudadmin`)。 在 ECE 配置文件中搜索, 然后在`UserName`元素中选择值。 `User Role="CloudAdmin"`
诊断连接字符串          | Azure 存储帐户的 SAS URL, 在测试执行过程中将在该帐户中复制诊断日志。 有关生成 SAS URL 的说明, 请参阅[生成诊断连接字符串](#generate-the-diagnostics-connection-string)。 |

> [!IMPORTANT]
> **诊断连接字符串**必须有效, 然后才能继续。

### <a name="generate-the-diagnostics-connection-string"></a>生成诊断连接字符串

在测试执行过程中存储诊断日志需要诊断连接字符串。 使用在安装过程中创建的 Azure 存储帐户 (请参阅[将验证设置为服务资源](azure-stack-vaas-set-up-resources.md)), 创建共享访问签名 (SAS) URL, 以允许 VaaS 访问权限, 以便将日志上传到存储帐户。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 从 "**允许的服务" 选项**中选择 " **Blob** "。 取消选择任何其他选项。

1. 从**允许的资源类型**中选择 "**服务**"、"**容器**" 和 "**对象**"。

1. 选择 "**读取**"、"**写入**"、"**列出** **"、** "从**允许的权限** **创建**"。 取消选择任何其他选项。

1. 将“开始时间”设置为当前时间，将“结束时间”设置为距当前时间三个月的未来时间。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> SAS URL 在生成 URL 时指定的结束时间过期。  
在计划测试时, 请确保 URL 有效期至少为30天, 并确保测试执行所需的时间 (建议三个月)。

## <a name="next-steps"></a>后续步骤

- 了解[作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)