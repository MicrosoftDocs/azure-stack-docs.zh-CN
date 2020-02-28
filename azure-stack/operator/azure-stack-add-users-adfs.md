---
title: 向 AD FS 中添加 Azure Stack 集线器用户
description: 了解如何为 Active Directory 联合身份验证服务（AD FS）部署添加 Azure Stack 集线器用户。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 79380d40282757e704f9dc0ef3bdaf977253ba14
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703343"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>在 Active Directory 联合身份验证服务中添加新的 Azure Stack 集线器用户帐户（AD FS）

你可以使用 " **Active Directory 用户和计算机**" 管理单元将其他用户添加到 Azure Stack 中心环境中，并将 AD FS 用作其标识提供者。

## <a name="add-windows-server-active-directory-users"></a>添加 Windows Server Active Directory 用户

1. 使用提供对 Windows 管理工具的访问权限的帐户登录到计算机，并打开一个新的 Microsoft 管理控制台（MMC）。
2. 选择 "**文件" > "添加或删除管理单元"** 。

   > [!TIP]
   > 将*directory 域*替换为与目录匹配的域。 

3. 选择**Active Directory 用户和计算机** > *Directory-域* > **用户**。
4. 选择 "**操作**" > **New** > **用户**"。
5. 在 "新建对象-用户" 中，提供用户详细信息。 选择“**下一页**”。
6. 提供并确认密码。
7. 选择 "**下一步**" 完成值。 选择 "**完成**" 以创建用户。


## <a name="next-steps"></a>后续步骤

[创建应用标识以访问 Azure Stack 集线器资源](azure-stack-create-service-principals.md)