---
title: 将服务主体凭据存储在 Azure Stack Key Vault |Microsoft Docs
description: 了解 Key Vault 如何存储 Azure Stack 上的服务主体凭据
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
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: efa8dda8061ce81d751e9cce47c5e81a3917f2bf
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138842"
---
# <a name="store-service-principal-credentials-in-azure-stack-key-vault"></a>将服务主体凭据存储在 Azure Stack Key Vault

开发 Azure Stack 上的应用程序通常需要创建服务主体和使用这些凭据部署之前进行身份验证。 但是，有时您丢失的服务主体的存储的凭据。 本文介绍如何创建服务主体，并将值存储在 Azure Key Vault 中以供日后检索。

有关 Key Vault 的详细信息，请参阅[此文](azure-stack-key-vault-intro.md)。

## <a name="prerequisites"></a>必备组件

- 包含 Azure Key Vault 服务的产品/服务的订阅。
- PowerShell 配置为与 Azure Stack 配合使用。

## <a name="key-vault-in-azure-stack"></a>Azure Stack 中的 Key Vault

在 Azure Stack 中的密钥保管库有助于保护加密密钥和机密的云应用和服务使用。 使用 Key Vault 可以加密密钥和机密。

若要创建 Key Vault，请执行以下步骤：

1. 登录到 Azure Stack 门户。

2. 在仪表板中，依次选择“+ 创建资源”、“安全 + 标识”、“Key Vault”。   

   ![创建 Key Vault](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. 在“创建密钥保管库”  窗格中，为保管库分配**名称**。 保管库名称只能包含字母数字字符和连字符 (-)， 它们不应以数字开头。

4. 从可用订阅列表中选择订阅。

5. 选择现有的资源组，或创建一个新的资源组。

6. 选择定价层。

7. 选择一个现有的访问策略，或创建一个新的访问策略。 使用访问策略可授予用户、应用程序或安全组对此保管库执行操作的权限。

8. （可选）选择“高级访问权限策略”以便能够访问所需功能。

9. 配置设置后，请选择“确定”  ，然后选择“创建”  。 Key Vault 部署随即开始。

## <a name="create-a-service-principal"></a>创建服务主体

1. 通过 Azure 门户登录到你的 Azure 帐户。

2. 依次选择“Azure Active Directory”、“应用注册”、“添加”。   

3. 提供应用名称和 URL。 选择任一**Web 应用 / API**或**本机**为你想要创建的应用程序的类型。 设置这些值后，选择“创建”  。

4. 依次选择“Active Directory”、“应用程序注册”、你的应用程序。  

5. 复制**应用程序 ID**并将其存储在应用代码中。 示例应用程序使用**客户端 ID**指**应用程序 ID**。

6. 若要生成身份验证密钥，请选择“密钥”。 

7. 提供的说明和密钥持续时间。

8. 选择“保存”。 

9. 复制单击“保存”后显示的**密钥**。 

## <a name="store-the-service-principal-inside-key-vault"></a>在 Key Vault 中存储服务主体

1. 登录 Azure Stack 的用户门户，选择前面创建的 Key Vault，然后选择“机密”磁贴。 

2. 在“机密”窗格中，选择“生成/导入”。  

3. 在中**创建机密**窗格中，选择**手动**从选项列表。 如果你已创建服务主体使用证书，从下拉列表中，选择的证书，然后上载该文件。

4. 输入从服务主体中复制的“应用程序 ID”作为密钥的名称。  密钥名称只能包含字母数字字符和连字符 (-)。

5. 将服务主体中的密钥值粘贴到“值”选项卡中。 

6. 选择“服务主体”作为“内容类型”。  

7. 设置密钥的“激活日期”和“过期日期”值。  

8. 选择“创建”以开始部署。 

已成功创建机密后，服务主体信息存储在此处。 可以在任何时候下选择**机密**并查看或修改其属性。 属性部分包含机密标识符，即外部应用程序使用来访问此机密统一资源标识符 (URI)。

## <a name="next-steps"></a>后续步骤

- [使用服务主体](azure-stack-create-service-principals.md)
- [通过门户管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-portal.md)  
- [使用 PowerShell 管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-powershell.md)