---
title: 验证 Azure Stack HCI 群集
description: 了解群集验证的重要性，以及何时在现有 Azure Stack HCI 群集上运行它。 探索用于解决已更新服务器群集问题的方案。
author: JohnCobb1
ms.author: v-johcob
ms.topic: article
ms.date: 10/2/2020
ms.openlocfilehash: 682e9063f6f04f5298e7cab4053af179e1c90cd7
ms.sourcegitcommit: 6ed6db8e393aace41586a0fba925dc297159d45e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91663935"
---
# <a name="validate-an-azure-stack-hci-cluster"></a>验证 Azure Stack HCI 群集

>适用于：Azure Stack HCI 版本 v20H2；Windows Server 2019

本操作指南文章重点介绍群集验证为何重要，以及何时在现有 Azure Stack HCI 群集上运行它。 建议对以下主要方案执行群集验证：
- 部署服务器群集后，请运行 DCB 工具以测试网络。
- 更新服务器群集后，根据方案，运行两个验证选项以排查群集问题。
- 在设置使用存储副本进行复制后，通过检查某些特定事件并运行几个命令来验证复制是否正常进行。
- 创建服务器群集后，请运行 DCB 工具，然后将其投入生产。

    若要了解有关如何部署 Azure Stack HCI 群集的详细信息，请参阅 [部署概述](deployment-overview.md)。

## <a name="what-is-cluster-validation"></a>什么是群集验证？
群集验证的目的是在群集投入生产之前找出硬件或配置的问题。 群集验证有助于确保你即将部署的 Azure Stack HCI 解决方案真正可靠。 你还可以在已配置的故障转移群集上使用群集验证作为诊断工具。

## <a name="specific-validation-scenarios"></a>特定验证方案
本部分介绍需要验证或验证有用的方案。

- **在配置群集之前进行验证：**
  - **准备成为故障转移群集的一组服务器：** 这是最直接的验证方案。 硬件组件（系统、网络和存储）已连接，但系统尚未作为群集运行。 在这种情况下运行测试不会影响可用性。
 
  - **服务器 VM：** 对于群集中的虚拟化服务器，请像在任何其他新群集上一样运行群集验证。 运行该功能的要求是相同的，无论是否具有：
    - 在两台物理计算机之间发生故障转移的“主机群集”。
    - 在同一台物理计算机上的来宾操作系统之间发生故障转移的“来宾群集”。
 
- **在配置并使用群集之后进行验证：**

  - **在将服务器添加到群集之前：** 向群集添加服务器时，强烈建议验证群集。 运行群集验证时，请同时指定现有群集成员和新服务器。
  
  - **添加驱动器时：** 在向群集添加其他驱动器时（这不同于替换故障的驱动器或创建依赖于现有驱动器的虚拟磁盘或卷），运行群集验证以确认新存储将正常运行。

  - **执行可影响固件或驱动程序的更改时：** 如果对群集进行可影响固件或驱动程序的升级或更改，则必须运行群集验证，以确认硬件、固件、驱动程序和软件的新组合支持故障转移群集功能。

  - **使用备份还原系统后：** 使用备份还原系统后，运行群集验证以确认系统作为群集的一部分正常工作。

## <a name="validate-networking"></a>验证网络
Microsoft Validate-DCB 工具用于验证群集上的数据中心桥接 (DCB) 配置。 为此，该工具将预期的配置作为输入，然后测试群集中的每个服务器。 本部分介绍如何安装和运行 Validate-DCB 工具、查看结果并解决该工具识别的网络错误。

在网络上，基于聚合以太网 (RoCE) 的远程直接内存访问 (RDMA) 需要 DCB 技术来实现无损的网络结构。 尽管 iWARP 不需要 DCB，但仍推荐使用。 但是，配置 DCB 可能很复杂，需要在以下位置进行精确配置：
- 群集中的每个服务器
- RDMA 流量通过的结构上的每个网络端口

### <a name="prerequisites"></a>必备条件
- 要验证的服务器群集的网络设置信息，包括：
    - 主机或服务器群集名称
    - 虚拟交换机名称
    - 网络适配器名称
    - 优先级流控制 (PFC) 和增强版传输选择 (ETS) 设置
- 用于从 Microsoft 下载 Windows PowerShell 中的工具模块的 Internet 连接。

### <a name="install-and-run-the-validate-dcb-tool"></a>安装并运行 Validate-DCB 工具
若要安装并运行 Validate-DCB 工具，请执行以下操作：
1. 在管理 PC 上，以管理员身份打开 Windows PowerShell 会话，然后使用以下命令安装该工具。

    ```powershell
    Install-Module Validate-DCB
    ```

1. 接受使用 NuGet 提供程序并访问存储库以安装该工具的请求。
1. 在 PowerShell 连接到 Microsoft 网络以下载该工具后，键入 `Validate-DCB`，然后按 Enter 键启动工具向导。

    > [!NOTE]
    > 如果无法运行 Validate-DCB 工具脚本，则可能需要调整 PowerShell 执行策略。 使用 Get-ExecutionPolicy cmdlet 查看当前脚本执行策略设置。 有关在 PowerShell 中设置执行策略的信息，请参阅[关于执行策略](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7)。

1. 在“欢迎使用 Validate-DCB 配置向导”页面上，选择“下一步”。
1. 在“群集和节点”页面上，键入要验证的服务器群集名称，选择“解析”将其列在页面上，然后选择“下一个” 。

    :::image type="content" source="../media/validate/clusters-and-nodes.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

1. 在“适配器”页上：
   1. 选中“附加的 vSwitch”复选框并键入该 vSwitch 的名称。
   1. 在“适配器名称”下，键入每个物理 NIC 的名称，在“主机 vNIC 名称”下，键入每个虚拟 NIC (vNIC) 的名称，在“VLAN”下，键入每个适配器使用的 VLAN ID  。
   1. 展开“RDMA 类型”下拉列表框并选择相应的协议：RoCE 或 iWARP 。 同时将“Jumbo 帧”设置为适合你的网络的值，然后选择“下一步” 。

    :::image type="content" source="../media/validate/adapters.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面" lightbox="../media/validate/adapters.png":::

    > [!NOTE]
    > - 要了解 SR-IOV 如何提高网络性能，请参阅[单个根 I/O 虚拟化 (SR-IOV) 概述](/windows-hardware/drivers/network/overview-of-single-root-i-o-virtualization--sr-iov-)。

1. 在“数据中心桥接”页面上，修改值以匹配组织的“优先级”、“策略名称”和“带宽保留”的设置，然后选择“下一步”   。

    :::image type="content" source="../media/validate/data-center-bridging.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面" lightbox="../media/validate/data-center-bridging.png":::

    > [!NOTE]
    > 在前一个向导页面上选择基于 RoCE 的 RDMA 需要 DCB 来保证所有 NIC 和交换机端口的网络可靠性。

1. 在“保存和部署”页面的“配置文件路径”框中，使用 .ps1 扩展名将配置文件保存到某个位置，以便在稍后需要时可以再次使用，然后选择“导出”以开始运行 Validate-DCB 工具 。

   - 你可以选择通过完成该页面的“将配置部署到节点”部分来部署配置文件，这包括使用 Azure 自动化帐户部署配置并对其进行验证的功能。 请参阅[创建 Azure 自动化帐户](/azure/automation/automation-quickstart-create-account)以开始使用 Azure 自动化。

    :::image type="content" source="../media/validate/save-and-deploy.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

### <a name="review-results-and-fix-errors"></a>查看结果并修复错误
Validate-DCB 工具产生两个单元的结果：
1. [全局单元]结果列出了运行模式测试的先决条件和要求。
1. [模式单元]结果提供关于每个群集主机配置和最佳做法的反馈。

此示例通过指示失败计数为 0 来表明单个服务器的所有先决条件和模式单元测试的扫描结果成功。

:::image type="content" source="../media/validate/global-unit-and-modal-unit-results.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

以下步骤演示了如何从 vNIC SMB02 识别大型数据包错误以及如何修复：
1. Validate-DCB 工具扫描的结果显示，失败计数错误为 1。

    :::image type="content" source="../media/validate/failed-count-error-1.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

1. 向后滚动结果显示一个红色标示的错误，指示主机 S046036 上 vNIC SMB02 的大型数据包被设置为默认大小 1514，但应该设置为 9014。

    :::image type="content" source="../media/validate/jumbo-packet-setting-error.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

1. 查看主机 S046036 上 vNIC SMB02 的高级属性，可以发现大型数据包被设置为默认“已禁用” 。

    :::image type="content" source="../media/validate/hyper-v-advanced-properties-jumbo-packet-setting.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

1. 修复错误需要启用大型数据包功能并将其大小更改为 9014 字节。 在主机 S046036 上再次运行扫描将通过返回失败计数 0 来确认此更改。

    :::image type="content" source="../media/validate/jumbo-packet-error-fix-confirmation.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

若要详细了解如何解决 DCB 工具标识的错误，请参阅以下视频。

> [!VIDEO https://www.youtube.com/embed/cC1uACvhPBs]

## <a name="validate-the-cluster"></a>验证群集
使用以下步骤验证 Windows Admin Center 中现有群集中的服务器。

1. 在 Windows Admin Center 的“所有连接”下，选择要验证的 Azure Stack HCI 群集，然后选择“连接” 。

    “群集管理器仪表板”显示群集的概述信息。

1. 在“群集管理器仪表板”的“工具”下，选择“服务器”  。
1. 在“库存”页面上，选择群集中的服务器，然后展开“更多”子菜单并选择“验证群集”  。
1. 在“验证群集”弹出窗口中，选择“是” 。

    :::image type="content" source="../media/validate/validate-cluster-pop-up.png" alt-text="Validate-DCB 配置向导的“群集和节点”页面":::

1. 在“凭证安全服务提供者(CredSSP)”弹出窗口中，选择“是” 。
1. 提供凭据以启用 CredSSP，然后选择“继续” 。<br> 群集验证在后台运行，并在完成时向你发出通知，此时你可以查看验证报告，如下一节所述。

> [!NOTE]
> 在验证群集服务器之后，出于安全原因，需要禁用 CredSSP。

### <a name="disable-credssp"></a>禁用 CredSSP
成功验证服务器群集后，出于安全目的，需要在每台服务器上禁用凭据安全支持提供者 (CredSSP) 协议。 有关详细信息，请参阅 [CVE-2018-0886](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-0886)。

1. 在 Windows Admin Center 中的“所有连接”下，选择群集中的第一个服务器，然后选择“连接” 。
1. 在“概述”页上，选择“禁用 CredSSP”，然后在“禁用 CredSSP”弹出窗口中，选择“是”   。

    步骤 2 的结果删除了服务器“概述”页面顶部的红色“CredSSP 已启用”横幅，并在其他服务器上禁用了 CredSSP 。

### <a name="view-validation-reports"></a>查看验证报告
现在，你可以查看群集验证报告。

可以通过多种方式来访问验证报告：
- 在“清单”页上，展开“更多”子菜单，然后选择“查看验证报告”  。


- 在 Windows Admin Center 的右上角，选择“通知”铃铛图标以显示“通知”窗格  。
选择“已成功验证群集”通知，然后选择“转到故障转移群集验证报告” 。

> [!NOTE]
> 服务器群集验证过程可能需要一些时间才能完成。 当进程正在运行时，不要切换到 Windows Admin Center 中的其他工具。 在“通知”窗格中，“验证群集”通知下方的状态栏指示进程何时完成 。

## <a name="validate-the-cluster-using-powershell"></a>使用 PowerShell 验证群集
你还可以使用 Windows PowerShell 在服务器群集上运行验证测试并查看结果。 可以在设置群集之前和之后运行测试。

若要在服务器群集上运行验证测试，请从管理 PC 发出 Get-Cluster 和 Test-Cluster <server clustername> PowerShell cmdlet，或者仅在群集上直接运行 Test-Cluster cmdlet  ：

```PowerShell
$Cluster = Get-Cluster -Name 'server-cluster1'
Test-Cluster -InputObject $Cluster -Verbose
```

如需更多示例和用法信息，请查看 [Test-Cluster](/powershell/module/failoverclusters/test-cluster?view=win10-ps) 参考文档。

## <a name="validate-replication-for-storage-replica"></a>验证存储副本的复制
如果使用存储副本在延伸群集中或在群集之间复制卷，则可以使用几个事件和 cmdlet 来获取复制的状态。 

在以下方案中，我们通过为两个站点创建复制组 (RG) 来配置存储副本，然后为 Site1 中的源服务器节点（Server1、Server2）和 Site2 中的目标（复制的）服务器节点（Server3、Server4）指定数据卷和日志卷。

若要确定 Site1 中 Server1 的复制进度，请运行 Get-WinEvent 命令并检查事件 5015、5002、5004、1237、5001 和 2200：

```powershell
Get-WinEvent -ComputerName Server1 -ProviderName Microsoft-Windows-StorageReplica -max 20
```

对于 Site2 中的 Server3，运行以下 `Get-WinEvent` 命令以查看显示伙伴关系创建情况的存储副本事件。 此事件会显示复制的字节数和所用的时间。 例如：

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | Where-Object {$_.ID -eq "1215"} | FL
```

对于 Site2 中的 Server3，运行 `Get-WinEvent` 命令并检查事件 5009、1237、5001、5015、5005 和 2200 以了解处理进度。 在该序列中不应有错误的警告。 将有许多 1237 事件，这些事件指示进度。

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

或者，副本的目标服务器组显示要始终复制的剩余字节数，并且可以通过 PowerShell 使用 `Get-SRGroup` 进行查询。 例如：

```powershell
(Get-SRGroup).Replicas | Select-Object numofbytesremaining
```

对于 Site2 中的节点 Server3，运行以下命令并检查事件 5009、1237、5001、5015、5005 和 2200，以了解复制进度。 应该没有错误警告。 但是，将有许多“1237”事件，这些事件只是指示进度。

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

作为不会终止的进度脚本：

```powershell
while($true) {
$v = (Get-SRGroup -Name "Replication2").replicas | Select-Object numofbytesremaining
[System.Console]::Write("Number of bytes remaining: {0}`r", $v.numofbytesremaining)
Start-Sleep -s 5
}
```

若要获取延伸群集中的复制状态，请使用 `Get-SRGroup` 和 `Get-SRPartnership`：

```powershell
Get-SRGroup -Cluster ClusterS1
```

```powershell
Get-SRPartnership -Cluster ClusterS1
```

```powershell
(Get-SRGroup).replicas -Cluster ClusterS1
```

确认站点之间的数据复制成功后，就可以创建 VM 和其他工作负载了。

## <a name="see-also"></a>另请参阅
- 使用 DiskSpd.exe 针对新创建的存储空间中的综合工作负载进行性能测试。 若要了解详细信息，请参阅[在 Windows Server 中使用综合工作负载测试存储空间性能](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn894707(v=ws.11))。
- Windows Server 评估是一项高级服务，适用于想要 Microsoft 检查其 Windows Server 2019 安装情况的客户。 有关详细信息，请与 Microsoft 顶级支持联系。 若要了解详细信息，请参阅 [Windows Server 按需评估（服务器、安全性、Hyper-V、故障转移群集、IIS）入门](/services-hub/health/getting-started-windows-server)。
