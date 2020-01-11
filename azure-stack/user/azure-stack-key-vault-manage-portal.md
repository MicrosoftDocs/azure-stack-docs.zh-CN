---
title: 使用门户管理 Azure Stack 中心的 Key Vault |Microsoft Docs
description: 了解如何使用 Azure Stack 中心门户管理 Azure Stack 中心的 Key Vault。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: sethm
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 1ba2be2d37cfa0b0d850f67124440fb9de13a23f
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879149"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-the-portal"></a>使用门户管理 Azure Stack 中心的 Key Vault

本文介绍如何使用 Azure Stack 中心门户在 Azure Stack 中心创建和管理密钥保管库。

## <a name="prerequisites"></a>必备组件

你必须订阅包含 Azure Key Vault 服务的产品/服务。

## <a name="create-a-key-vault"></a>创建 key vault

1. 登录到[用户门户](https://portal.local.azurestack.external)。

2. 在仪表板中，选择 " **+ 创建资源**"，然后**安全 + 标识**" **Key Vault**"。

    ![Key Vault 屏幕](media/azure-stack-key-vault-manage-portal/image1.png)

3. 在 "**创建 Key Vault** " 窗格中，为保管库分配一个**名称**。 保管库名称只能包含字母数字字符和连字符（-）字符。 它们不能以数字开头。

4. 从可用订阅列表中选择一个**订阅**。 提供 Key Vault 服务的所有订阅都显示在下拉列表中。

5. 选择现有资源组，或创建新**资源组**。

6. 选择**定价层**。 在 Azure Stack 开发工具包（ASDK）中，密钥保管库仅支持**标准**sku。

7. 选择一个现有访问策略或创建一个新的**访问策略**。 使用访问策略可以授予用户、应用或安全组对此保管库执行操作的权限。

8. 还可以选择**高级访问策略**来启用对功能的访问。 例如：用于部署的虚拟机（Vm）、用于模板部署的资源管理器，以及对用于卷加密的 Azure 磁盘加密的访问。

9. 配置设置后，请选择 **"确定"** ，然后选择 "**创建**"。 此步骤将启动密钥保管库部署。

## <a name="manage-keys-and-secrets"></a>管理密钥和机密

创建密钥保管库后，请使用以下过程来创建和管理保管库中的密钥和机密：

### <a name="create-a-key"></a>创建密钥

1. 登录到 Azure Stack 集线器[用户门户](https://portal.local.azurestack.external)。

2. 在仪表板中，选择 "**所有资源**"，选择先前创建的密钥保管库，然后选择 "**密钥**" 磁贴。

3. 在 "**密钥**" 窗格中，选择 "**生成/导入**"。

4. 在 "**创建密钥**" 窗格中，从**选项**列表中选择要用于创建密钥的方法。 你可以**生成**新的密钥、**上传**现有密钥，或使用**还原备份**来选择密钥的备份。

5. 输入密钥的**名称**。 密钥名称只能包含字母数字字符和连字符（-）字符。

6. （可选）为密钥配置**设置激活日期**和**设置到期日期**值。

7. 选择 "**创建**" 以开始部署。

成功创建密钥后，可以在 "**密钥**" 下选择密钥并查看或修改其属性。 Properties 节包含**密钥标识符**，它是外部应用用于访问此密钥的统一资源标识符（URI）。 若要限制对此密钥的操作，请在 "**允许的操作**" 下配置设置。

![URI 密钥](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>创建机密

1. 登录到[用户门户](https://portal.local.azurestack.external)。

2. 在仪表板中，选择 "**所有资源**"，选择先前创建的密钥保管库，然后选择 "**机密**" 磁贴。

3. 在 "**机密**" 下，选择 "**添加**"。

4. 在 "**创建机密**" 下，从 "**上载选项**" 列表中，选择要为其创建密钥的选项。 如果输入机密值，或者从本地计算机上载**证书**，则可以**手动**创建机密。

5. 输入密钥的**名称**。 机密名称只能包含字母数字字符和连字符（-）字符。

6. （可选）指定**内容类型**，并为机密配置**设置激活日期**和**设置到期日期**的值。

7. 选择 "**创建**" 以开始部署。

成功创建机密后，可以在 "**机密**" 下选择它并查看或修改其属性。 **机密标识符**是外部应用可用于访问此机密的 URI。

![URI 机密](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>后续步骤

* [通过检索存储在 Key Vault 中的密码来部署 VM](azure-stack-key-vault-deploy-vm-with-secret.md)
* [使用存储在 Key Vault 中的证书部署 VM](azure-stack-key-vault-push-secret-into-vm.md)
