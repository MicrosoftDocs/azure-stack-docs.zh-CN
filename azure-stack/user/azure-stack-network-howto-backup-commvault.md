---
title: 如何通过 Commvault 在 Azure Stack 集线器上备份 VM |Microsoft Docs
description: 了解如何通过 Commvault 在 Azure Stack 集线器上备份 VM。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: 5210e3874653c268eb72e8ce26c675e2494a1fe9
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819788"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>通过 Commvault 在 Azure Stack 集线器上备份 VM

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>使用 Commvault 备份 VM 概述

本文逐步讲解如何配置 Commvault 实时同步，以更新位于单独 Azure Stack 集线器缩放单位上的恢复 VM。 本文详细介绍如何配置常见合作伙伴解决方案来保护和恢复 Azure Stack 集线器上部署的虚拟机的数据和系统状态。

下图显示了使用 Commvault 备份 Vm 时的总体解决方案。

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

本文内容：

1. 在源 Azure Stack 中心实例上创建运行 Commvault 软件的 VM。

2. 在辅助位置中创建存储帐户。 本文假设你将在单独的 Azure Stack 中心实例（目标）中的存储帐户中创建一个 Blob 容器，并且可以从源 Azure Stack 集线器访问目标 Azure Stack 中心。

3. 在源 Azure Stack 中心实例上配置 Commvault，并将源 Azure Stack 中心的 Vm 添加到 VM 组。

4. 配置 Commvault 的 LifeSync。

你还可以下载并提供兼容的合作伙伴 VM 映像，以保护 Azure Stack 中心 Vm 与 Azure 云或其他 Azure Stack 中心。 本文将介绍如何通过 Commvault 实时同步来保护 VM。

此方法的拓扑如下图所示：

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.png)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>创建 Commvault VM 窗体 Commvault Marketplace 项

1. 打开 Azure Stack 集线器用户门户。

2. 选择 "**创建资源** > **计算**" > **Commvault**。

    > [!Note]  
    > 如果 Commvault 不可用，请与云操作员联系。

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. 配置 "创建虚拟机" 中的基本设置 **，1个基础知识**：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 输入“名称”。

    b.保留“数据库类型”设置，即设置为“共享”。 选择 "**标准 HHD**"。
    
    c. 输入**用户名**。
    
    d.单击“下一步”。 输入**密码**。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 确认密码。
    
    f. 选择要备份的**订阅**。
    
    g. 选择**资源组**。
    
    h. 选择 Azure Stack 集线器的**位置**。 如果使用的是 ASDK，请选择 "**本地**"。
    
    i. 选择“确定”。

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. 选择 Commvault VM 的大小。 用于备份的 VM 大小至少应为 10 GB RAM 和 100 GB 的存储空间。

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png)。

5. 选择 Commvault VM 的设置。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 将可用性设置为**None**。
    
    b.保留“数据库类型”设置，即设置为“共享”。 对于 "使用托管磁盘"，选择 **"是"** 。
    
    c. 为**虚拟网络**选择默认 VNet。
    
    d.单击“下一步”。 选择默认**子网**。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择默认的 "**公共 IP 地址**"。
    
    f. 将 VM 保留在 "**基本**" 网络安全组中。
    
    g. 打开 HTTP （80）、HTTPS （443）、SSH （22）和 RDP （3389）端口。
    
    h. 选择 "**无扩展**"。
    
    i. 选择 "为**启动诊断** **启用**"。
    
    j. 将 "**来宾 OS 诊断**" 设置为 "**已禁用**"。
    
    k. 保留默认**诊断存储帐户**。
    
    l. 选择“确定”。

6. 验证完 Commvault VM 后，查看其摘要。 选择“确定”。

## <a name="get-your-service-principal"></a>获取服务主体

你需要了解你的身份管理器是 Azure AD 还是 AD DFS。 下表包含在 Azure Stack 集线器中设置 Commvault 时所需的信息。

| 元素 | Description | 源 |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 资源管理器 URL | Azure Stack 中心资源管理器终结点。 | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-hub-resource-manager-endpoint |
| 应用程序名称 |  |  |
| 应用程序 ID | 在本文上一部分中创建服务主体时保存的服务主体应用 ID。 | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| 订阅 ID | 使用订阅 ID 可以访问 Azure Stack 集线器中的产品/服务。 | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| 租户 ID （目录 ID） | Azure Stack 中心租户 ID。 | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| 应用程序密码 | 在创建服务主体时保存的服务主体应用程序机密。 | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>使用 Commvault 控制台配置备份

1. 打开 RDP 客户端，并连接到 Azure Stack 中心内的 Commavult VM。 输入凭据。

2. 在 Commvault VM 上安装 Azure Stack 集线器 PowerShell 和 Azure Stack 中心工具。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 有关安装 Azure Stack 集线器 PowerShell 的说明，请参阅[安装适用于 Azure Stack 中心的 PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json)。  
    b.保留“数据库类型”设置，即设置为“共享”。 有关安装 Azure Stack 集线器工具的说明，请参阅[从 GitHub 下载 Azure Stack 中心工具](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908)。

3. 在 Commvault VM 中安装 Commvault 后，打开 Commcell 控制台。 从 "开始" 中，选择**Commvault** > **Commvault Commcell 控制台**。

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. 将备份存储库配置为使用 Commvault Commcell 控制台中 Azure Stack 中心外部的存储。 在 CommCell 浏览器中，选择 "存储资源" > 存储池 "。 右键单击并选择 "**添加存储池"。** 选择“云”。

5. 添加存储池的名称。 选择“**下一页**”。

6. 选择 "**创建** > **云存储**"。

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. 选择云服务提供商。 在此过程中，我们将使用另一个 Azure Stack 集线器。 选择 Microsoft Azure 存储。

8. 选择 Commvault VM 作为 "MediaAgent"。

9. 输入存储帐户的访问信息。 可在此处找到有关设置 Azure 存储帐户的说明。 访问信息：

    -  **服务主机**：从资源的 Blob 容器属性中获取 URL 的名称。 例如，我的 URL 是 https：\//backuptest.blob.westus.stackpoc.com/mybackups 和我使用的 blob.westus.stackpoc.com 在服务主机中。
    
    -   **帐户名**：使用存储帐户名称。 可以在存储资源的 "访问密钥" 边栏选项卡中找到此项。
    
    -   **访问密钥**：从存储资源中的 "访问密钥" 边栏选项卡获取访问密钥。
    
    -   **容器**：容器的名称。 在本例中为 mybackups。
    
    -   **存储类**：保留为用户容器的默认存储类。

10. 按照[创建 Microsoft Azure Stack 中心客户端](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm)中的说明创建 Microsoft Azure Stack 集线器客户端

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. 选择要保护的 Vm 或资源组，并附加备份策略。

12. 配置备份计划以符合恢复的 RPO 要求。

13. 执行第一次完整备份。

## <a name="configure-commvault-live-sync"></a>配置 Commvault 实时同步 

有两个选项可供你选择。 你可以选择从备份的主要副本复制更改，或将更改从辅助副本复制到恢复 VM。 从备份集进行复制可消除对源计算机的读取 IO 影响。

1. 在配置实时同步期间，你将需要提供源 Azure Stack 中心（虚拟服务器代理）和目标 Azure Stack 中心详细信息。

2. 有关配置 Commvault 实时同步的步骤，请参阅[Microsoft Azure Stack 中心的实时同步复制](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm)。

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. 在配置实时同步期间，你将需要提供目标 Azure Stack 中心和虚拟服务器代理详细信息。

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. 继续配置并添加将在其中托管副本磁盘的目标存储帐户、将在其中放置副本 Vm 的资源组，以及要附加到副本 Vm 的名称。

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. 还可以通过选择每个 VM 旁的 "**配置**" 来更改 vm 大小和配置网络设置。

6. 将复制频率设置为目标 Azure Stack 中心

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. 检查你的设置以保存配置。 然后，将创建恢复环境，并将在所选间隔开始复制。


## <a name="set-up-failover-behavior-using-live-sync"></a>使用实时同步设置故障转移行为

Commvault 实时同步可让你将计算机从一个 Azure Stack 集线器故障转移到另一个集线器，并故障回复以恢复原始 Azure Stack 中心的操作。 工作流是自动执行和记录的。

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

选择要故障转移到恢复 Azure Stack 中心的 Vm，然后选择计划内或计划外的故障转移。 当在恢复站点中恢复操作之前有时间正常关闭生产环境时，计划的故障转移是合适的。 计划的故障转移将关闭生产 Vm，将最后的更改复制到恢复站点，并使恢复 Vm 以最新数据联机，并应用在实时同步配置期间指定的 VM 大小和网络配置。 非计划的故障转移将尝试关闭生产 Vm，但在生产环境不可用时将继续，并只需将上次接收到的复制数据集与应用到 VM 的数据和大小和网络先前所选的配置。 下图演示了一个未计划的故障转移，其中恢复 Vm 已通过 Commvault 实时同步进入联机状态。

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)  