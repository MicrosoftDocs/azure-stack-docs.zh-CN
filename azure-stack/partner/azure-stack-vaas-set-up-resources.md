---
title: 教程-将验证的资源设置为服务
description: 在本教程中，了解如何将用于验证的资源设置为服务。
author: mattbriggs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7c47c6810802cce31793aae3be3a1502acb5f102
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143928"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>教程：将验证的资源设置为服务

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

验证即服务（VaaS）是一项 Azure 服务，用于在市场中验证和支持 Azure Stack 集线器解决方案。 使用服务验证解决方案之前，请遵循此文。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备好使用 VaaS，方法是设置 Azure Active Directory （AD）。
> * 创建存储帐户。

## <a name="configure-an-azure-ad-tenant"></a>配置 Azure AD 租户

Azure AD 租户用于注册组织，并使用 VaaS 对用户进行身份验证。 合作伙伴将使用租户的基于角色的访问控制（RBAC）功能来管理合作伙伴组织中的哪些用户可以使用 VaaS。 有关详细信息，请参阅[什么是 Azure Active Directory？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。

### <a name="create-a-tenant"></a>创建租户

创建组织将用于访问 VaaS 服务的租户。 使用描述性名称，例如 `ContosoVaaS@onmicrosoft.com`。

1. 在[Azure 门户](https://portal.azure.com)中创建 Azure AD 租户，或使用现有租户。 <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. 将组织的成员添加到租户。 这些用户将负责使用该服务来查看或计划测试。 完成注册后，将定义用户的访问级别。

    通过分配下列角色之一，授权租户中的用户运行 VaaS 中的操作：

    | 角色名称 | 说明 |
    |---------------------|------------------------------------------|
    | 所有者 | 对所有资源具有完全访问权限。 |
    | 读取器 | 可以查看所有资源，但不能创建或管理。 |
    | 测试参与者 | 可以创建和管理测试资源。 |

    若要在**Azure Stack Hub 验证服务**应用程序中分配角色：

   1. 登录 [Azure 门户](https://portal.azure.com)。
   2. 选择 "**标识**" 部分下的 "**所有服务**" > **Azure Active Directory** 。
   3. 选择 "**企业应用程序**" > **Azure Stack 中心验证服务**应用程序。
   4. 选择“用户和组”。 " **Azure Stack 集线器验证服务-用户和组**" 边栏选项卡列出有权使用该应用程序的用户。
   5. 选择 " **+ 添加用户**" 以从租户中添加用户并分配角色。

      如果要将 VaaS 资源和操作隔离到组织内的不同组中，可以创建多个 Azure AD 租户目录。

### <a name="register-your-tenant"></a>注册租户

此过程通过**Azure Stack 中心验证服务**Azure AD 应用程序来授权租户。

1. 在[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)将有关租户的以下信息发送给 Microsoft。

    | 数据 | 说明 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 组织名称 | 官方组织名称。 |
    | Azure AD 租户目录名称 | 要注册的 Azure AD 租户目录名称。 |
    | Azure AD 租户目录 ID | 与目录关联的 Azure AD 租户目录 GUID。 有关如何查找 Azure AD 租户目录 ID 的信息，请参阅[获取租户 id](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in)。 |

2. 请等待 Azure Stack 中心验证团队确认你的租户可以使用 VaaS 门户。

### <a name="consent-to-the-vaas-application"></a>同意 VaaS 应用程序

作为 Azure AD 管理员，请为 VaaS Azure AD 应用程序代表租户提供所需的权限：

1. 使用租户的全局管理员凭据登录到[VaaS 门户](https://azurestackvalidation.com/)。 

2. 选择 **"我的帐户"** 。

3当提示向 VaaS 列出的 Azure AD 权限时，接受这些条款以继续。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

在测试执行过程中，VaaS 会将诊断日志输出到 Azure 存储帐户。 除了测试日志以外，还可以使用存储帐户上传包验证工作流的 OEM 扩展包。

Azure 存储帐户托管在 Azure 公有云中，而不是托管在 Azure Stack 中心环境中。

1. 在 Azure 门户中，选择 "**所有服务**" > **存储** > **存储帐户**"。 在 "**存储帐户**" 边栏选项卡中，选择 "**添加**"。

2. 选择要在其中创建存储帐户的订阅。

3. 在“资源组”下，选择“新建”。 输入新资源组的名称。

4. 查看 Azure 存储帐户的[命名约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#storage)。 输入存储帐户的名称。

5. 为存储帐户选择 "**美国西部**" 区域。

    为了确保在存储日志时不会产生网络费用，可以将 Azure 存储帐户配置为仅使用**美国西部**区域。 数据复制和热存储层功能对于此数据不是必需的。 启用任一功能会大幅增加成本。

6. 将设置保留为默认值（**帐户类型**除外）：

    - 默认情况下，"**部署模型**" 字段设置为**资源管理器**。
    - “性能”字段默认设置为“标准”。
    - 选择 "**帐户类型**" 字段作为**Blob 存储**。
    - 默认情况下，"**复制" 字段**设置为 "**本地冗余存储（LRS）** "。
    - “访问层”默认设置为“热”。

7. 选择“查看+创建”可查看存储帐户设置并创建帐户。

## <a name="next-steps"></a>后续步骤

如果你的环境不允许入站连接，请遵循部署本地代理以在硬件上运行测试的教程。

> [!div class="nextstepaction"]
> [部署本地代理](azure-stack-vaas-local-agent.md)
