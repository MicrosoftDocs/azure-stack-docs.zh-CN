---
title: 添加或删除 Azure Stack HCI 群集的服务器
description: 了解如何在 Azure Stack HCI 的群集中添加或删除服务器节点
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/29/2021
ms.openlocfilehash: 0d8aa3cc6418c8e64b7a8dc97290757c8be41856
ms.sourcegitcommit: e56b0eaf92c633d5d782bfdf17ce521fa88a7256
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227401"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>添加或删除 Azure Stack HCI 群集的服务器

> 适用于：Azure Stack HCI，版本 20H2

可以在 Azure Stack HCI 的群集中轻松地添加或删除服务器。 请记住，在 CPU 类型、内存、驱动器数量以及驱动器的类型和大小方面，每个新的物理服务器必须与群集中的其他服务器严格匹配。

无论何时添加或删除服务器，都还必须在之后执行群集验证，以确保群集正常运行。 这同时适用于非拉伸和拉伸群集。

## <a name="before-you-begin"></a>在开始之前

第一步是从原始 OEM 获取新的 HCI 硬件。 添加要在群集中使用的新服务器硬件时，请始终参考 OEM 提供的文档。

1. 将新的物理服务器置于机架中并进行正确的布线。
1. 启用物理交换机端口，并根据情况调整访问控制列表 (ACL) 和 VLAN ID。
1. 按照 OEM 说明在基板管理控制器 (BMC) 中配置正确的 IP 地址，并应用所有 BIOS 设置。
1. 使用 OEM 提供的工具，将当前的固件基线应用于所有组件。
1. 运行 OEM 验证测试，以确保硬件同质化现有的群集服务器。
1. 在新服务器上安装 Azure Stack HCI 操作系统。 有关详细信息，请参阅 [部署 AZURE STACK HCI](../deploy/operating-system.md)。
1. 将服务器加入群集域。

## <a name="add-a-server-to-a-cluster"></a>将服务器添加到群集

使用 Windows 管理中心将服务器加入群集。

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="“添加服务器”屏幕" lightbox="media/manage-cluster/add-server.png":::

1. 在“Windows 管理中心”中，从顶部下拉箭头中选择“群集管理器”。
1. 在“群集连接”下，选择该群集。
1. 在“工具”下，选择“服务器”。
1. 在“服务器”下，选择“库存”选项卡。
1. 在“库存”选项卡上，选择“添加”。
1. 在“服务器名称”中，输入要添加的服务器的完全限定域名，单击“添加”，然后再次单击底部的“添加”。
1. 验证服务器是否已成功添加到群集。

## <a name="remove-a-server-from-a-cluster"></a>从群集中删除服务器

从群集中删除服务器的步骤类似于向群集添加服务器的步骤。

请记住，删除服务器时，也会删除与该服务器关联的所有虚拟机、驱动器和工作负荷。

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="“删除服务器”对话框" lightbox="media/manage-cluster/remove-server.png":::

1. 在“Windows 管理中心”中，从顶部下拉箭头中选择“群集管理器”。
1. 在“群集连接”下，选择该群集。
1. 在“工具”下，选择“服务器”。
1. 在“服务器”下，选择“库存”选项卡。
1. 在“库存”选项卡上，选择要删除的服务器，然后选择“删除”。
1. 若要同时从存储池中删除任何服务器驱动器，请启用该复选框。
1. 验证服务器是否已成功从群集中删除。

每当在群集中添加或删除服务器时，请确保随后运行群集验证测试。

## <a name="add-server-pairs-to-a-stretched-cluster"></a>向拉伸群集添加服务器对

拉伸群集要求每个站点中有相同数量的服务器节点和相同数量的驱动器。 向拉伸群集添加服务器对时，会立即将其驱动器添加到拉伸群集中两个站点的存储池中。 如果每个站点上的存储池的大小与添加时的大小不同，系统会将其拒绝。 这是因为存储池的大小在两个站点中必须相同。

向延伸群集添加服务器节点时，请花几分钟时间观看视频：

> [!VIDEO https://www.youtube.com/embed/AVHPkRmsZ5Y]

使用 Windows PowerShell 在延伸群集中添加或删除服务器。 使用 [ClusterFaultDomainXML](/powershell/module/failoverclusters/get-clusterfaultdomainxml) 和 [ClusterFaultDomainXML](/powershell/module/failoverclusters/set-clusterfaultdomainxml) cmdlet 时，你首先修改站点（容错域）信息，然后再添加服务器。

然后，你可以使用 [Add-ClusterNode](/powershell/module/failoverclusters/add-clusternode) cmdlet 同时向每个站点添加服务器对，还可以同时添加每台新服务器的驱动器。

通常，你可以从远程计算机（而不是群集中的服务器）管理群集。 此远程计算机称为管理计算机。

> [!NOTE]
> 从某一管理计算机运行 PowerShell 命令时，请将 `-Cluster` 参数与你管理的群集的名称配合使用。

好了，让我们开始吧：

1. 使用以下 PowerShell cmdlet 确定群集的状态：

    返回群集中活动服务器的列表：

     ```powershell
    Get-ClusterNode
    ```

    返回群集存储池的统计信息：

    ```powershell
    Get-StoragePool pool*
    ```

    列出哪些服务器位于哪个站点（容错域）：

    ```powershell
    Get-ClusterFaultDomain
    ```

1. 在记事本或其他文本编辑器中打开 `Sites.xml` 文件：

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. 在你的管理电脑本地导航到 `Sites.xml` 文件所在的位置，打开该文件。 `Sites.xml` 文件类似于以下内容：

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
        </Site>
    <Topology>
    ```

1. 使用此示例，你可以向每个站点添加服务器（`Server5`、`Server6`），如下所示：

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
            <Node Name="Server5" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
            <Node Name="Server6" Description="" Location="">
        </Site>
    <Topology>
    ```

1. 修改当前站点（容错域）信息。  第一个命令设置一个变量来获取 `Sites.xml` 文件的内容并输出该内容。 第二个命令根据变量 `$XML` 来设置修改。

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. 验证你所做的修改是否正确：

    ```
    Get-ClusterFaultDomain
    ```

1. 使用 `Add-ClusterNode` cmdlet 向群集添加服务器对：

    ```
    Add-ClusterNode -Name Server5,Server6
    ```

成功添加服务器后，关联的驱动器会自动添加到每个站点的存储池。 最后，运行状况服务会创建一个存储作业来包括新的驱动器。

## <a name="remove-server-pairs-from-a-stretched-cluster"></a>从拉伸群集中删除服务器对

从拉伸群集中删除服务器对类似于添加服务器对的过程，但使用的是 [Remove-ClusterNode](/powershell/module/failoverclusters/remove-clusternode) cmdlet。

1. 使用以下 PowerShell cmdlet 确定群集的状态：

    返回群集中活动服务器的列表：

     ```powershell
    Get-ClusterNode
    ```

    返回群集存储池的统计信息：

    ```powershell
    Get-StoragePool pool*
    ```

    列出哪些服务器位于哪个站点（容错域）：

    ```powershell
    Get-ClusterFaultDomain
    ```

1. 在记事本或其他文本编辑器中打开 `Sites.xml` 文件：

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. 使用前面的示例，在 `Sites.xml` 文件中删除每个站点的 `<Node Name="Server5" Description="" Location="">` 和 `<Node Name="Server6" Description="" Location="">` XML 条目。
1. 使用以下两个 cmdlet 修改当前站点（容错域）信息：

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. 验证你所做的修改是否正确：

    ```
    Get-ClusterFaultDomain
    ```
1. 使用 `Remove-ClusterNode` cmdlet 从群集中删除服务器对：

    ```
    Remove-ClusterNode -Name Server5,Server6
    ```

成功删除服务器后，关联的驱动器会自动从站点池中删除。 最后，运行状况服务会创建一个存储作业来删除这些驱动器。

## <a name="next-steps"></a>后续步骤

- 添加或删除服务器后，应当验证群集。 有关详细信息，请参阅[验证群集](../deploy/validate.md)。