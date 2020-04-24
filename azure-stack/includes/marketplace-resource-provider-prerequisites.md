---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e1e2c3c6c3d1149b2cd034c2f5e4abfdf26e712f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423978"
---
如果已从 Azure Stack 中心市场安装了资源提供程序，则可能已完成了常见的先决条件，可以跳过本部分。 否则，请先完成以下先决条件： 

1. [向 Azure 注册 Azure Stack 中心实例](../operator/azure-stack-registration.md)（如果尚未这样做）。 此步骤是必需的，因为你将从 Azure 连接到 marketplace 并将其下载到 marketplace。

2. 如果你不熟悉 Azure Stack 中心管理员门户的**Marketplace 管理**功能，请参阅[从 Azure 下载 marketplace 项并发布到 Azure Stack 中心](../operator/azure-stack-download-azure-marketplace-item.md)。 本文逐步讲解如何将项目从 Azure 下载到 Azure Stack 中心市场。 它涵盖连接和断开连接的方案。 如果 Azure Stack 集线器实例断开连接或部分连接，则在准备安装之前需要完成其他先决条件。

3. 更新 Azure Active Directory （Azure AD）主目录。 从版本1910开始，必须使用新的部署资源提供程序（DRP）应用程序来注册主目录租户。 此应用将允许 DRP 成功创建和注册资源提供程序。 如果此步骤未完成，则资源提供程序的安装将失败。 

   - 成功将 Azure Stack 集线器实例更新到1910或更高版本后，请按照[克隆/下载 Azure Stack 中心工具存储库中的说明进行操作](../operator/azure-stack-powershell-download.md)。 
   - 然后，按照[更新 Azure Stack 中心 Azure AD 主目录的说明进行操作（在安装更新或新建资源提供程序之后）](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers)。 