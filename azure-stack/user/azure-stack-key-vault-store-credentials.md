---
title: 将服务主体凭据存储在 Azure Stack 中心 Key Vault
description: 了解 Key Vault 如何在 Azure Stack 集线器上存储服务主体凭据
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 01/16/2020
ms.openlocfilehash: 66d69fa3397fa748dfbc67377c3e52c47f35904c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702714"
---
# <a name="store-service-principal-credentials-in-azure-stack-hub-key-vault"></a>将服务主体凭据存储在 Azure Stack 中心 Key Vault

在 Azure Stack 中心开发应用程序通常需要创建服务主体，并使用这些凭据在部署之前进行身份验证。 但是，有时您丢失了服务主体的存储凭据。 本文介绍如何创建服务主体，并将这些值存储在 Azure Key Vault 中以便以后检索。

有关 Key Vault 的详细信息，请参阅[Azure Stack 中心的 Key Vault 简介](azure-stack-key-vault-intro.md)。

## <a name="prerequisites"></a>必备条件

- 包含 Azure Key Vault 服务的产品/服务的订阅。
- PowerShell 已安装并配置为与 Azure Stack 中心一起使用。

## <a name="key-vault-in-azure-stack-hub"></a>Azure Stack 中心中的 Key Vault

Azure Stack 中心 Key Vault 可帮助保护云应用和服务使用的加密密钥和机密。 通过使用 Key Vault，可以加密密钥和机密。

若要创建密钥保管库，请执行以下步骤：

1. 登录到 Azure Stack 中心门户。

2. 在仪表板中选择 " **+ 创建资源**"，然后**安全 + 标识**" **Key Vault"。**

   ![创建 Key Vault](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. 在 "**创建 Key Vault** " 窗格中，为保管库分配一个**名称**。 保管库名称只能包含字母数字字符和连字符（-）字符。 它们不能以数字开头。

4. 从可用订阅列表中选择一个订阅。

5. 选择现有的资源组，或创建一个新的组。

6. 选择定价层。

7. 选择一个现有的访问策略，或者创建一个新的访问策略。 使用访问策略可以授予用户、应用程序或安全组对此保管库执行操作的权限。

8. 还可以选择高级访问策略来启用对功能的访问。

9. 配置设置后，请选择 **"确定"** ，然后选择 "**创建**"。 密钥保管库部署开始。

## <a name="create-a-service-principal"></a>创建服务主体

1. 通过 Azure 门户登录到 Azure 帐户。

2. 依次选择 " **Azure Active Directory**"、"**应用注册**"、"**添加**"。

3. 提供应用的名称和 URL。 选择要创建的应用类型的 " **Web 应用"/"API** " 或 "**本机**"。 设置这些值后，选择“创建”。

4. 选择 " **Active Directory**"，然后选择 "应用**注册**"，并选择应用程序。

5. 复制**应用程序 ID**并将其存储在应用程序代码中。 引用**应用程序 id**时，示例应用使用**客户端 id** 。

6. 若要生成身份验证密钥，请选择“密钥”。

7. 提供密钥的说明和持续时间。

8. 选择“保存”。

9. 复制在单击 "**保存**" 后变为可用的**密钥**。

## <a name="store-the-service-principal-inside-key-vault"></a>将服务主体存储在 Key Vault

1. 登录到 Azure Stack 中心的用户门户，选择之前创建的密钥保管库，然后选择 "**机密**" 磁贴。

2. 在 "**密钥**" 窗格中，选择 "**生成/导入**"。

3. 在 "**创建密钥**" 窗格中，从选项列表中选择 "**手动**"。 如果已使用证书创建服务主体，请从下拉列表中选择证书，然后上传文件。

4. 输入从服务主体复制的**应用程序 ID**作为密钥名称。 密钥名称只能包含字母数字字符和连字符（-）字符。

5. 将密钥值从服务主体粘贴到 "**值**" 选项卡中。

6. 选择**内容类型**的 "**服务主体**"。

7. 为密钥设置**激活日期**和**到期日期**值。

8. 选择 "**创建**" 以开始部署。

成功创建密钥后，会将服务主体信息存储在此处。 你可以随时选择**密码**，并查看或修改其属性。 **Properties**节包含机密标识符，它是外部应用用于访问此机密的统一资源标识符（URI）。

## <a name="next-steps"></a>后续步骤

- [使用服务主体](azure-stack-create-service-principals.md)
- [通过门户管理 Azure Stack 中心的 Key Vault](azure-stack-key-vault-manage-portal.md)  
- [使用 PowerShell 管理 Azure Stack 中心的 Key Vault](azure-stack-key-vault-manage-powershell.md)
