---
title: 更新 Windows Defender 防病毒
titleSuffix: Azure Stack
description: 了解如何在 Azure Stack 上更新 Windows Defender 防病毒
services: azure-stack
author: justinha
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 97a9fe73eb83df07a9b24dc130c8295218cda7ad
ms.sourcegitcommit: 53f7daf295783a30feb284d4c48c30c6936557c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74831014"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>更新 Azure Stack 集线器上的 Windows Defender 防病毒

[Windows Defender 防病毒](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)是一个提供安全和病毒保护的反恶意软件解决方案。 每个 Azure Stack 基础结构组件（Hyper-v 主机和虚拟机）都受 Windows Defender 防病毒保护。 若要获得最新保护，需要定期更新 Windows Defender 防病毒软件定义、引擎和平台。 如何应用更新取决于你的配置。

## <a name="connected-scenario"></a>已连接的场景

Azure Stack 中心[更新资源提供程序](azure-stack-updates.md#the-update-resource-provider)每天下载反恶意软件定义和引擎更新。 每个 Azure Stack 基础结构组件从更新资源提供程序获取更新，并自动应用更新。

对于连接到公共 Internet 的 Azure Stack 中心部署，将应用[每月 Azure Stack 更新](azure-stack-apply-updates.md)。 每月 Azure Stack 中心更新包括每月的 Windows Defender 防病毒平台更新。

## <a name="disconnected-scenario"></a>断开连接方案

对于未连接到公共 Internet 的 Azure Stack 中心部署（例如，气流数据中心），从1910版开始，客户可以在发布反恶意软件定义和引擎更新时应用这些部署。 

若要将更新应用于你的 Azure Stack 中心解决方案，首先必须从 Microsoft 网站下载它们（下面的链接），然后将其导入到*updateadminaccount*下的存储 blob 容器中。 计划任务每30分钟扫描一次 blob 容器，并且如果找到新的 Defender 定义和引擎更新，则会将其应用到 Azure Stack 中心基础结构。 

对于那些尚未在1910或更高版本上的已断开连接的部署，或者每日不能下载 Defender 定义和引擎更新的情况，每月 Azure Stack 中心更新包含 Windows Defender 防病毒定义、引擎和每月的平台更新。 


### <a name="set-up-windows-defender-for-manual-updates"></a>为手动更新设置 Windows Defender 

在1910版本中，将两个新 cmdlet 添加到了特权终结点，以在 Azure Stack 中心配置 Windows Defender 手动更新。 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

下面的过程演示如何设置 Windows Defender 手动更新。 

1. 连接到特权终结点，并运行以下 cmdlet，以指定将在其中上载 Defender 更新的存储 blob 容器的名称。 

   > [!NOTE] 
   > 下面所述的手动更新过程仅适用于不允许访问 "go.microsoft.com" 的已断开连接的环境。 尝试在连接环境中运行 cmdlet AzsDefenderManualUpdate 将导致错误。 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. 下载这两个 Windows Defender 更新包，并将其保存在可从 Azure Stack 中心管理门户访问的位置。  

   * [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64)中的 mpam-fe 
   * [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094)中的 nis_full 

   > [!NOTE] 
   > **每次**需要更新 Defender 签名时，必须下载这两个文件。 

3. 在管理门户中，选择 "**所有服务**"。 然后，在 "**数据 + 存储**" 类别下，选择 "**存储帐户**"。 （或者，在 "筛选器" 框中，开始键入**存储帐户**，并选择它。） 

   ![Azure Stack 集线器 Defender-所有服务](./media/azure-stack-security-av/image1.png)  

4. 在 "筛选器" 框中，键入 "**更新**"，然后选择 " **updateadminaccount** " 存储帐户。 

5. 在存储帐户详细信息中的 "**服务**" 下，选择 " **blob**"。 

   ![Azure Stack 集线器 Defender-blob](./media/azure-stack-security-av/image2.png) 

6. 在 " **Blob 服务**" 下，选择 " **+ 容器**" 创建容器。 输入用 AzsDefenderManualUpdate 指定的名称（在本示例中为*defenderupdates*），然后选择 **"确定"** 。 

   ![Azure Stack 中心 Defender-容器](./media/azure-stack-security-av/image3.png) 

7. 创建容器后，单击容器名称，然后单击 "**上载**"，将包文件上传到该容器。 

   ![Azure Stack 中心 Defender-上传](./media/azure-stack-security-av/image4.png) 

8. 在 "**上传 blob**" 下，单击 "文件夹" 图标，浏览到 Windows Defender 更新*mpam-fe*文件，然后单击 "文件资源管理器" 窗口中的 "**打开**"。 

9. 在 "**上传 blob**" 下，单击 "**上载**"。 

   ![Azure Stack 中心 Defender-上传 blob1](./media/azure-stack-security-av/image5.png) 

1. 对*nis_full 的 .exe*文件重复步骤8和9。 

   ![Azure Stack 中心 Defender-上传 blob2](./media/azure-stack-security-av/image6.png)

计划任务每30分钟扫描一次 blob 容器，并应用任何新的 Windows Defender 包。  

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Stack 中心安全性](azure-stack-security-foundations.md)
