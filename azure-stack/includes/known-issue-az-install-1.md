---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: 003f6801209d5d5ab1c9c4bd1df0fa47578004ee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935132"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>安装 Az 模块时引发错误

- 适用：此问题适用于2002及更高版本
- 原因：安装模块时，将引发错误。 错误消息开始： `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` 或错误消息可能包含以下文本： `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- 修正：在同一会话中运行以下 cmdlet：  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
关闭会话并启动新的提升的 PowerShell 会话。
- 发生次数：通用