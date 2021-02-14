---
title: 特权访问工作站和特权终结点访问
description: 了解特权访问工作站和特权终结点访问
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b17bf72931d5e80f9cfebe6df54655856695dba2
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487980"
---
# <a name="privileged-access-workstation-and-privileged-endpoint-access"></a>特权访问工作站和特权终结点访问

## <a name="overview"></a>概述

对于此过程，你必须连接到特权访问工作站 (PAW) 。 客户需要为你提供使用远程桌面连接到 PAW 的能力。

## <a name="configuring-the-winrm"></a>配置 WinRM

若要允许从 PAW 连接到特权终结点，请确保在 PAW 上将特权终结点 IP Azure Stack 地址设置为受信任的主机。 从管理员门户获取这些 IP 地址的说明是在验证第16页上的 "缩放单元节点访问" 和 "运行状况"。

若要查看或编辑 WinRM 受信任的主机，请启动提升的 PowerShell 会话：

-   查看受信任的主机。

若要查看当前受信任的主机，请在 PowerShell 中运行：

-   编辑受信任的主机。

如果 () ERCS 的紧急恢复控制台服务器不存在，请运行以下内容，为受信任的主机设置新值，并将 * \< ERCS01_IP \* 、* \< ERCS02_IP \* 和 * \< ERCS03_IP 替换为 \* 在 Azure Stack 中心管理门户中定义的三个特权终结点 ip：

## <a name="connect-to-the-privileged-endpoint"></a>连接到特权终结点

在 PAW 上，打开提升的 PowerShell 会话，并运行以下两个命令。 将 * \< ERCS_IP 替换为 \* 某个特权终结点实例的 IP，如本过程前面所述。 出现提示时，请输入特权终结点 (PEP) 客户提供的凭据。

## <a name="close-the-privileged-endpoint"></a>关闭特权终结点

若要关闭特权终结点会话，请运行以下内容：

## <a name="further-reading"></a>延伸阅读

若要详细了解如何连接到特权终结点并使用这些终结点，请参阅[使用 Azure Stack 中心中的特权终结点](../../operator/azure-stack-privileged-endpoint.md) 
 [](../../operator/azure-stack-privileged-endpoint.md)。