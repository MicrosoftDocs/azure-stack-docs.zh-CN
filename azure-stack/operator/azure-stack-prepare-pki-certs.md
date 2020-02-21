---
title: 准备 Azure Stack 中心 PKI 证书以进行部署或轮换
titleSuffix: Azure Stack Hub
description: 了解如何为 Azure Stack 中心集成系统部署或在现有 Azure Stack 中心环境中轮替机密准备 PKI 证书。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/16/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: 321ceff84ec24997866366c533eb31939acde2dd
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509715"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>准备 Azure Stack 中心 PKI 证书以进行部署或轮换

必须导入和导出[从证书颁发机构（CA）获取的](azure-stack-get-pki-certs.md)证书文件，其中包含与 Azure Stack 中心证书要求匹配的属性。

## <a name="prepare-certificates-for-deployment"></a>为部署准备证书

使用以下步骤来准备和验证 Azure Stack 中心 PKI 证书，这些证书将用于部署新的 Azure Stack 中心环境或在现有的 Azure Stack 中心环境中轮换机密。

### <a name="import-the-certificate"></a>导入证书

1. 将所[选 CA 获取](azure-stack-get-pki-certs.md)的原始证书版本复制到部署主机上的某个目录中。 
   > [!WARNING]
   > 不要以任何方式从 CA 直接提供的文件中复制已导入、导出或更改的文件。

1. 右键单击证书并选择 "**安装证书**" 或 "**安装 PFX**"，具体取决于证书从 CA 传递的方式。

1. 在**证书导入向导**中，选择 "**本地计算机**" 作为导入位置。 选择“**下一页**”。 在以下屏幕上，再次选择 "下一步"。

    ![证书的本地计算机导入位置](./media/prepare-pki-certs/1.png)

1. 选择 **"将所有证书放入下列存储"** ，然后选择 "**企业信任**" 作为位置。 选择 **"确定"** 以关闭 "证书存储选择" 对话框，然后选择 "**下一步**"。

   ![配置证书存储以进行证书导入](./media/prepare-pki-certs/3.png)

   a. 如果要导入 PFX，将会显示一个附加对话框。 在 "**私钥保护**" 页上，输入证书文件的密码，并启用 "将**此密钥标记为可导出"。这允许您在以后备份或传输密钥**。 选择“**下一页**”。

   ![标记密钥为可导出](./media/prepare-pki-certs/2.png)

1. 选择 "**完成**" 以完成导入。

> [!NOTE]
> 为 Azure Stack 中心导入证书后，证书的私钥将作为 PKCS 12 文件（PFX）存储在群集存储上。

### <a name="export-the-certificate"></a>导出证书

打开 "证书管理器" MMC 控制台并连接到 "本地计算机" 证书存储。

1. 打开 Microsoft 管理控制台。 若要在 Windows 10 中打开控制台，请右键单击 "**开始" 菜单**，选择 "**运行**"，然后键入**mmc** ，然后按 enter。

2. 选择 "**文件**" > "**添加/删除管理单元**"，然后选择 "**证书**" 并选择 "**添加**"。

    ![在 Microsoft 管理控制台中添加证书管理单元](./media/prepare-pki-certs/mmc-2.png)

3. 选择 "**计算机帐户**"，然后选择 "**下一步**"。 选择 "**本地计算机**"，然后单击 "**完成**"。 选择 **"确定"** 以关闭 "添加/删除管理单元" 页。

    ![为 Microsoft 管理控制台中的 "添加证书" 管理单元选择帐户](./media/prepare-pki-certs/mmc-3.png)

4.  > **证书位置**浏览到 "**证书** > **企业信任**"。 验证你的证书是否在右侧显示。

5. 从 "证书管理器控制台" 任务栏中，选择 "**操作**" > **所有任务**" > **导出**"。 选择“**下一页**”。

   > [!NOTE]
   > 根据你拥有的 Azure Stack 中心证书数量，你可能需要多次完成此过程。

6. 选择 **"是，导出私钥**"，然后单击 "**下一步**"。

7. 在 "导出文件格式" 部分中：
    
   - 选择 **"包括证书中的所有证书（如果可能）"** 。  
   - 选择 "**导出所有扩展属性**"。  
   - 选择 "**启用证书隐私**"。  
   - 单击“下一步”。  
    
     ![包含选定选项的证书导出向导](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. 选择 "**密码**"，并为证书提供密码。 创建符合以下密码复杂性要求的密码：

    * 最小长度为8个字符。
    * 以下至少三个：大写字母、小写字母、0-9 的数字、特殊字符、不大写或小写的字母字符。

    请记下此密码。 您将使用它作为部署参数。

9. 选择“**下一页**”。

10. 选择要导出的 PFX 文件的文件名和位置。 选择“**下一页**”。

11. 选择“完成”。

## <a name="next-steps"></a>后续步骤

[验证 PKI 证书](azure-stack-validate-pki-certs.md)