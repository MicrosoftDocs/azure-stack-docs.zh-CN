---
title: 安装 Visual Studio 并连接到 Azure Stack 集线器
description: 了解如何安装 Visual Studio 并连接到 Azure Stack 集线器。
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2020
ms.openlocfilehash: 9fb0cf281fb97bc5cf255fb39507869b106d0a1b
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702952"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>安装 Visual Studio 并连接到 Azure Stack 集线器

可以使用 Visual Studio 来编写 Azure 资源管理器[模板](azure-stack-arm-templates.md)并将其部署到 Azure Stack 中心。 本文中的步骤介绍了如何在[Azure Stack 集线器](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)上或在外部计算机上安装 Visual Studio （如果你计划通过[VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)使用 Azure Stack 中心）。

## <a name="install-visual-studio"></a>安装 Visual Studio

1. 下载并运行[Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。  

2. 打开**Microsoft Web 平台安装程序**。

3. 搜索**带有 MICROSOFT AZURE SDK-2.9.6 的 Visual Studio 社区 2015**。 单击 "**添加**"，然后单击 "**安装**"。

4. 卸载作为 Azure SDK 的一部分安装的**Microsoft Azure PowerShell** 。

    ![WebPI 安装步骤的屏幕截图](./media/azure-stack-install-visual-studio/image1.png)

5. [为 Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)。

6. 安装完成后，请重新启动操作系统。

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>通过 Azure AD 连接到 Azure Stack 集线器

1. 启动 Visual Studio。

2. 在“视图”菜单中，选择“云资源管理器”。

3. 在 "新建" 窗格中，选择 "**添加帐户**"，然后用你的 Azure Active Directory （Azure AD）凭据登录。  

    ![登录并连接到 Azure Stack 中心后的 Cloud Explorer 屏幕截图](./media/azure-stack-install-visual-studio/image2.png)

登录后，可以[部署模板](azure-stack-deploy-template-visual-studio.md)或浏览可用的资源类型和资源组，以创建自己的模板。  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>通过 AD FS 连接到 Azure Stack 集线器

1. 启动 Visual Studio。

2. 从 "**工具**" 中，选择 "**选项**"。

3. 展开**导航窗格**中的 "**环境**"，然后选择 "**帐户**"。

4. 选择 "**添加**"，并输入用户 Azure 资源管理器终结点。 对于 Azure Stack 开发工具包（ASDK），URL 是： `https://management.local.azurestack/external`。  对于 Azure Stack 集线器集成系统，URL 是： `https://management.[Region}.[External FQDN]`。

    ![添加新的 Azure 云发现终结点](./media/azure-stack-install-visual-studio/image5.png)

5. 选择 **添加** 。  

    Visual Studio 将调用 Azure 资源管理器，并发现 Azure 目录联合服务（AD FS）的终结点，包括身份验证终结点。

    ![登录并连接到 Azure Stack 中心后的 Cloud Explorer 屏幕截图](./media/azure-stack-install-visual-studio/image6.png)

6. 从 "**视图**" 菜单中选择 " **Cloud Explorer** "。

7. 选择 "**添加帐户**"，然后用你的 AD FS 凭据登录。  

    ![登录到 Cloud Explorer 中的 Visual Studio](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer 查询可用订阅。 你可以选择要管理的可用订阅。

    ![选择要管理的订阅 Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. 浏览现有资源、资源组或部署模板。

## <a name="next-steps"></a>后续步骤

- 详细了解 Visual [studio 与其他 Visual studio 版本](/visualstudio/install/install-visual-studio-versions-side-by-side)。
- [为 Azure Stack 集线器开发模板](azure-stack-develop-templates.md)。
