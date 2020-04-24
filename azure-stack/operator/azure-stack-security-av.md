---
title: 更新 Windows Defender 防病毒
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 上更新 Windows Defender 防病毒
author: justinha
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 8f0ad292f8d9772c53c332d2cad7af8bd606594a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77697733"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>在 Azure Stack Hub 上更新 Windows Defender 防病毒

[Windows Defender 防病毒](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)是一种反恶意软件解决方案，可提供安全性和病毒防护功能。 每个 Azure Stack Hub 基础结构组件（Hyper-V 主机和虚拟机）均受到 Windows Defender 防病毒的保护。 为了获得最新的保护，需要定期更新 Windows Defender 防病毒定义、引擎和平台。 如何应用更新取决于配置。

## <a name="connected-scenario"></a>已连接的场景

Azure Stack Hub 的[更新资源提供程序](azure-stack-updates.md#the-update-resource-provider)每天多次下载反恶意软件定义和引擎更新。 每个 Azure Stack Hub 基础结构组件都会从更新资源提供程序获取更新并自动应用更新。

对于连接到公共 Internet 的 Azure Stack Hub 部署，请应用[每月 Azure Stack Hub 更新](azure-stack-apply-updates.md)。 每月的 Azure Stack Hub 更新包括该月的 Windows Defender 防病毒平台更新。

## <a name="disconnected-scenario"></a>离线场景

对于未连接到公共 Internet 的 Azure Stack Hub 部署（例如，与网络隔绝的数据中心），从版本 1910 开始，客户可以应用已发布的反恶意软件定义和引擎更新。 

若要将更新应用到 Azure Stack Hub 解决方案，必须先从 Microsoft 网站（以下链接）下载更新，然后将其导入 *updateadminaccount* 下的存储 Blob 容器中。 计划的任务每隔 30 分钟扫描 Blob 容器，如果发现新的 Defender 定义和引擎更新，则会将其应用到 Azure Stack Hub 基础结构。 

对于尚未使用 1910 或更高版本或无法每日下载 Defender 定义和引擎更新的离线部署，每月的 Azure Stack Hub 更新包含该月的 Windows Defender 防病毒定义、引擎和平台更新。 


### <a name="set-up-windows-defender-for-manual-updates"></a>设置 Windows Defender 以进行手动更新 

在版本 1910 中，已将两个新的 cmdlet 添加到特权终结点，用于在 Azure Stack Hub 中配置 Windows Defender 手动更新。 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

以下过程说明如何设置 Windows Defender 手动更新。 

1. 连接到特权终结点，然后运行以下 cmdlet 来指定要将 Defender 更新上传到的存储 Blob 容器的名称。 

   > [!NOTE] 
   > 下述手动更新过程仅适用于不允许访问“go.microsoft.com”的离线环境。 尝试在联网环境中运行 Set-AzsDefenderManualUpdate cmdlet 会导致出错。 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. 下载两个 Windows Defender 更新包，并将其保存到可从 Azure Stack Hub 管理门户访问的位置。  

   * [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 中的 mpam-fe.exe 
   * [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 中的 nis_full.exe 

   > [!NOTE] 
   > **每当**你要更新 Defender 签名时，都必须下载这两个文件。 

3. 在管理门户中，选择“所有服务”。  然后，在“数据 + 存储”  类别下，选择“存储帐户”  。 （或者，在筛选框中开始键入“存储帐户”  ，然后选择它。） 

   ![Azure Stack Hub Defender - 所有服务](./media/azure-stack-security-av/image1.png)  

4. 在筛选框中，键入“更新”  ，然后选择 **updateadminaccount** 存储帐户。 

5. 在存储帐户详细信息中，在“服务”  下，选择 **Blob**。 

   ![Azure Stack Hub Defender - Blob](./media/azure-stack-security-av/image2.png) 

6. 在“Blob 服务”下，选择“+ 容器”以创建容器。   输入通过 Set-AzsDefenderManualUpdate 指定的名称（在本示例中为 *defenderupdates*），然后选择“确定”。  

   ![Azure Stack Hub Defender - 容器](./media/azure-stack-security-av/image3.png) 

7. 创建容器后，单击容器名称，然后单击“上传”  将包文件上传到容器。 

   ![Azure Stack Hub Defender - 上传](./media/azure-stack-security-av/image4.png) 

8. 在“上传 Blob”下，单击文件夹图标，浏览到 Windows Defender 更新 **mpam-fe.exe** 文件，然后在文件资源管理器窗口中单击“打开”。   

9. 在“上传 blob”  下，单击“上传”  。 

   ![Azure Stack Hub Defender - 上传 Blob 1](./media/azure-stack-security-av/image5.png) 

1. 针对 *nis_full.exe* 文件重复步骤 8 和 9。 

   ![Azure Stack Hub Defender - 上传 Blob 2](./media/azure-stack-security-av/image6.png)

计划的任务每隔 30 分钟扫描 Blob 容器，并应用任何新的 Windows Defender 包。  

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Stack Hub 安全性](azure-stack-security-foundations.md)
