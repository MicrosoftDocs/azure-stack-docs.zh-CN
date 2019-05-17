---
title: 为 Azure Stack 集成系统部署或机密轮换准备 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍如何为 Azure Stack 集成系统准备 Azure Stack PKI 证书。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 682a5ce693bd9f184fd73a322ab9ed29c2d90fae
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782498"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>准备在部署或轮换时使用的 Azure Stack PKI 证书

证书文件[从所选 CA 获取](azure-stack-get-pki-certs.md)必须导入和导出属性与匹配 Azure Stack 的证书要求。

## <a name="prepare-certificates-for-deployment"></a>为部署准备证书

使用以下步骤来准备和验证 Azure Stack PKI 证书，此类证书将用于部署新的 Azure Stack 环境，或者用于在现有的 Azure Stack 环境中轮换机密： 

### <a name="import-the-certificate"></a>导入证书

1. 将[从所选 CA 获取](azure-stack-get-pki-certs.md)的原始证书版本复制到部署主机上的目录。 
   > [!WARNING]
   > 如果已以任何方式导入、导出或更改直接由 CA 提供的文件，请勿复制该文件。

1. 右键单击证书并选择“安装证书”或“安装PFX”，具体取决于从 CA 传送证书的方式。

1. 在**证书导入向导**中，选择“本地计算机”作为导入位置。 选择“**下一步**”。 在下一个屏幕上，再次单击“下一步”。

    ![本地计算机导入位置](./media/prepare-pki-certs/1.png)

1. 选择“将所有证书放在以下存储中”，然后选择“企业信任”作为位置。 单击“确定”以关闭“证书存储选择”对话框，然后单击“下一步”。

   ![配置证书存储](./media/prepare-pki-certs/3.png)

   a. 如果要导入 PFX，将看到其他对话框。 在“私钥保护”页上，输入证书文件的密码，然后启用“将此密钥标记为可导出。这可让你在稍后备份或传输密钥”选项。 选择“**下一步**”。

   ![将密钥标记为可导出](./media/prepare-pki-certs/2.png)

1. 单击“完成”以完成导入。

### <a name="export-the-certificate"></a>导出证书

打开证书管理员 MMC 控制台并连接到本地计算机证书存储。

1. 打开 Microsoft 管理控制台，在 Windows 10 中右键单击“开始”菜单，然后单击“运行”。 键入 **mmc**，单击“确定”。

1. 单击“文件”、“添加/删除管理单元”，然后选择“证书”，单击“添加”。

    ![“添加证书”管理单元](./media/prepare-pki-certs/mmc-2.png)
 
1. 选择“计算机帐户”，单击“下一步”，选择“本地计算机”，然后单击“完成”。 单击“确定”以关闭“添加/删除管理单元”页。

    ![“添加证书”管理单元](./media/prepare-pki-certs/mmc-3.png)

1. 浏览到“证书”>“企业信任”>“证书位置”。 确认在右侧看到你的证书。

1. 从证书管理员控制台的任务栏中，选择“操作” > “所有任务” > “导出”。 选择“**下一步**”。

   > [!NOTE]
   > 根据你拥有 Azure Stack 证书的数量，可能需要多次完成此过程。

1. 选择“是，导出私钥”，并单击“下一步”。

1. 在“导出文件格式”部分执行以下操作：
    
   - 选择“包括证书路径中的所有证书(如果可能)”。  
   - 选择“导出所有扩展属性”。  
   - 选择“启用证书隐私”。  
   - 单击“下一步”。  
    
     ![包含选定选项的证书导出向导](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. 选择“密码”并为证书提供密码。 创建满足以下密码复杂性要求的密码。 最小长度为 8 个字符。 密码必须至少包含以下项中的三项：大写字母、小写字母、0-9 中的数字、特殊字符、不是大写也不是小写的字母字符。 记下此密码。 需将它用作部署参数。

1. 选择“**下一步**”。

1. 选择要导出的 pfx 文件的文件名和位置。 选择“**下一步**”。

1. 选择“完成”。

## <a name="next-steps"></a>后续步骤

[验证 PKI 证书](azure-stack-validate-pki-certs.md)