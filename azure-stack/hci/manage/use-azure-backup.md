---
title: 使用 Azure 备份来备份 Windows Server
description: 本文介绍了如何通过 Windows Admin Center 使用 Azure 备份来备份 Windows Server。
author: thomasmaurer
ms.author: thmaure
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: a4668e4d78a2ea3fb9f94913863d3471313034e5
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947497"
---
# <a name="use-azure-backup-to-back-up-windows-servers"></a>使用 Azure 备份来备份 Windows Server

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

Windows 管理中心会简化将 Windows Server 备份到 Microsoft Azure。 此过程还可防止意外或恶意删除以及损坏，甚至可以防止勒索软件。 若要自动进行设置，请将 Windows Admin Center 连接到 Azure 以使用 Azure 备份服务。

本文介绍了如何从 Windows Admin Center 配置 Azure 备份，以及为服务器卷和 Windows 系统状态创建备份策略。 本指南适用于将 Azure Stack HCI 操作系统上的虚拟机 (VM) 中运行的工作负载备份到 Azure。

若要详细了解 Azure 与 Windows Admin Center 的集成，请参阅[将 Windows Server 连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/)。

## <a name="how-azure-backup-works-with-windows-admin-center"></a>Azure 备份如何与 Windows Admin Center 协同工作
**Azure 备份**是可以用来在 Azure 中备份、保护和还原数据的一项服务。 Azure 备份将现有的本地或异地备份解决方案替换为可靠、安全且性价比高的基于云的解决方案。

若要了解详细信息，请参阅[什么是 Azure 备份服务？](/azure/backup/backup-overview)

Azure 备份提供多个组件，可将其下载并部署到适当的计算机、服务器或云中。 依据要保护的内容选择部署的组件或代理。 无论你是要保护本地数据还是 Azure 中数据，都可以使用所有 Azure 备份组件将数据备份到 Azure 的恢复服务保管库中。

Azure 备份与 Windows Admin Center 的集成非常适用于备份本地 Windows Server 或虚拟服务器的卷和 Windows 系统状态。 此综合性过程会备份文件服务器、域控制器和 IIS Web 服务器。

你可以通过“备份”工具访问 Windows Admin Center 中的 Azure 备份。 使用“备份”工具的设置、管理和监视功能，你可以快速开始备份服务器、执行备份和还原操作，以及监视 Windows Server 的总体备份运行状况。

## <a name="prerequisites"></a>必备条件
若要使用 Azure 备份，需要满足以下必备条件：
- 具有至少一个有效订阅的 Azure 帐户
- 目标 Windows Server 必须能够通过 Internet 访问 Azure
- 从 Windows Admin Center 网关到 Azure 的连接

    若要了解详细信息，请参阅[配置 Azure 集成](/windows-server/manage/windows-admin-center/azure/azure-integration)。

## <a name="getting-started-with-azure-backup"></a>Azure 备份入门
首次在 Windows Admin Center 中选择“备份”工具与 Azure 建立服务器连接时，会显示“欢迎使用 Azure 备份”页。 选择“设置 Azure 备份”以启动 Azure 备份设置向导。 以下各部分详细说明了向导中的步骤。

如果已为服务器连接配置了 Azure 备份，则选择“备份”工具会打开“备份仪表板”。 若要了解可以从仪表板执行的操作和任务，请参阅[管理和监视](#management-and-monitoring)部分。

### <a name="step-1-log-on-to-the-microsoft-azure-portal"></a>步骤1：登录到 Microsoft Azure 门户
登录 Azure 帐户。

> [!NOTE]
> 如果你已将 Windows Admin Center 网关连接到 Azure，则你应当会自动登录到门户。 选择“注销”，然后以其他用户身份登录。

### <a name="step-2-set-up-azure-backup"></a>步骤 2：设置 Azure 备份
为 Azure 备份选择以下设置：
- **订阅 ID:** 所需的 Azure 订阅，可用于将 Windows Server 备份到 Azure。 将创建所有 Azure 资产（例如 Azure 资源组和恢复服务保管库），并将其与所选订阅相关联。
- **保管库:** 恢复服务保管库是将存储服务器备份的位置。 你可以使用现有保管库，或者让 Windows Admin Center 新建一个。  
- **资源组：** Azure 资源组是资源集合的容器。 恢复服务保管库创建于或包含在指定的资源组中。 你可以使用现有资源组，或者让 Windows Admin Center 新建一个。
- **位置：** 将在其中创建恢复服务保管库的 Azure 区域。 建议选择与要备份的 Windows Server 距离最近的 Azure 区域。

### <a name="step-3-select-backup-items-and-schedule"></a>步骤 3：选择备份项和计划
1. 选择要从服务器备份的项。 Windows Admin Center 允许你选择卷和 Windows 系统状态的组合，并提供你选择进行备份的数据的估计大小。

    > [!NOTE]
    > 第一个备份是所有选定数据的完整备份。 后续备份是增量备份，只传输自上次备份以来发生的数据更改。

1. 从为你的 Windows 系统状态和卷预设的多个**备份计划**中进行选择。

### <a name="step-4-enter-an-encryption-passphrase"></a>步骤 4：输入加密密码
1. 输入最少包含 16 个字符的**加密密码**。 Azure 备份使用你创建和管理的加密密码来保护你的备份数据。 若要通过 Azure 备份来恢复数据，需要提供加密密码。

    > [!NOTE]
    > 将密码存储在安全的异地位置，例如其他服务器或 [Azure Key Vault](/azure/key-vault/quick-create-portal)。 如果丢失或忘记了通行短语，Microsoft 将不会存储该通行短语，也无法检索或重置密码。

1. 查看向导页上的所有设置，然后选择“应用”。

然后，Windows Admin Center 执行以下操作：
1. 创建 Azure 资源组（如果尚不存在）。
1. 根据指定的信息创建 Azure 恢复服务保管库。
1. 在保管库中安装并注册 Microsoft Azure 恢复服务代理。
1. 根据与 Windows Server 关联的选定选项创建备份和保留计划。

## <a name="management-and-monitoring"></a>监视和管理
成功设置 Azure 备份后，当你打开现有服务器连接的“备份”工具时，会看到“备份仪表板”。 可以从该仪表板执行以下任务：
- **访问 Azure 中的保管库：** 在“概览”选项卡上，选择“恢复服务保管库”以访问保管库，你可以在其中执行许多管理操作。 若要了解详细信息，请参阅[监视和管理恢复服务保管库](/azure/backup/backup-azure-manage-windows-server)。
- **执行即席备份：** 选择“立即备份”进行即席备份。 
- **监视作业并配置警报通知：** 导航到“作业”选项卡可监视正在进行的或过去的作业，并可[配置警报通知](/azure/backup/backup-azure-manage-windows-server#configuring-notifications-for-alerts)以接收有关失败的作业和其他备份警报的电子邮件。
- **查看恢复点并恢复数据：** 选择“恢复点”选项卡可以查看恢复点，选择“恢复数据”可以了解从 Azure 恢复数据的步骤。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：
- [使用 Azure Site Recovery 保护 Azure Stack HCI VM](./azure-site-recovery.md)
