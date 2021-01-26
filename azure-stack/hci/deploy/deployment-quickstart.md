---
title: 使用快速入门创建 Azure Stack HCI 群集并将其注册到 Azure
description: 了解如何部署 Azure Stack HCI，使用 Windows 管理中心创建群集，并将其注册到 Azure。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/22/2021
ms.openlocfilehash: 6527623ba5b42f40eb24cd6269f5d45bbccb0d25
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772231"
---
# <a name="quickstart-create-an-azure-stack-hci-cluster-and-register-it-with-azure"></a>快速入门：创建 Azure Stack HCI 群集并将其注册到 Azure

> 适用于：Azure Stack HCI，版本 20H2

在本快速入门中，你将了解如何部署两个服务器的单站点 Azure Stack HCI 群集并将其注册到 Azure。 有关多站点部署，请参阅 [延伸群集概述](../concepts/stretched-clusters.md)。

## <a name="before-you-start"></a>开始之前

创建群集之前，请执行以下操作：

* 通过预装了 Azure Stack HCI 操作系统的首选 Microsoft 硬件合作伙伴，从 [AZURE STACK HCI 目录](https://hcicatalog.azurewebsites.net) 中购买两个服务器。 查看 [系统要求](../concepts/system-requirements.md) ，确保你选择的硬件支持你计划在群集上运行的工作负荷。 建议使用具有高速网络适配器的系统，该系统使用 iWARP 进行简单配置。
* 创建一个用户帐户，该帐户是每个服务器上的本地管理员组的成员。
* [获取 Azure 订阅](https://azure.microsoft.com/)（如果还没有）。
* 在管理 PC 上[安装 Windows 管理中心](/windows-server/manage/windows-admin-center/deploy/install)，并[向 Azure 注册 windows 管理中心](../manage/register-windows-admin-center.md)。 请注意，你的管理计算机必须加入到你要在其中创建群集的同一 Active Directory 域或完全受信任的域。
* 记下你的部署的服务器名称、域名、IP 地址和 VLAN ID。

## <a name="create-the-cluster"></a>创建群集

按照以下步骤创建一个简单的双节点单站点群集。 有关更多详细信息或要创建延伸群集的详细信息，请参阅 [使用 Windows 管理中心创建 AZURE STACK HCI 群集](create-cluster.md)。

1. 在 Windows Admin Center 中，在“所有连接”下单击“添加” 。
1. 在“添加资源”面板中，在“Windows Server 群集”下选择“新建”。  
1. 在“选择群集类型”下，选择“Azure Stack HCI”。
1. 在 " **选择服务器位置**" 下，选择 **一个站点中的所有服务器**。
1. 单击“创建”。 现在，你将看到 "创建群集" 向导。 如果显示 " **凭据安全服务提供程序 (CredSSP)** 弹出窗口，请选择 **" 是 "** 以暂时启用它。 

创建群集向导有五个部分，每个部分都有多个步骤。

1. **入门。** 在本部分中，你将检查先决条件，添加服务器，加入域，安装所需的功能和更新，然后重新启动服务器。 
2. 网络。 向导的此部分将验证是否启用了正确的网络适配器并禁用未使用的任何网络适配器。 你将选择管理适配器、设置虚拟交换机配置，并通过提供 IP 地址来定义你的网络。
3. **群集.** 本部分验证你的服务器是否具有一致的配置，适用于群集，并创建实际群集。
4. **储存.** 接下来，你将清理并检查驱动器、验证存储并启用存储空间直通。
5. **SDN.** 你可以跳过第5节，因为我们不会对此群集使用软件定义的网络 (SDN) 。

如果在向导中启用了 CredSSP 协议，则出于安全考虑，需要在每个服务器上禁用该协议。

1. 在 Windows Admin Center 中，在“所有连接”下选择刚创建的群集。
1. 在“工具”下，选择“服务器”。
1. 在右窗格中，选择群集中的第一台服务器。
1. 在“概览”下，选择“禁用 CredSSP”。 此时会看到顶部的红色“CredSSP 已启用”横幅消失。
1. 为群集中的第二个服务器重复步骤3和4。

## <a name="set-up-a-cluster-witness"></a>设置群集见证

设置见证资源是必需的，因此，如果群集中的某个服务器进入脱机状态，则不会导致其他节点也无法使用。 对于本快速入门，我们将使用另一台服务器上的 SMB 文件共享作为见证服务器。 如果群集中的所有服务器节点都具有可靠的 internet 连接，则你可能更愿意使用 Azure 云见证。 有关见证选项的详细信息，请参阅 [设置分类见证](../manage/witness.md)。

1. 在 Windows Admin Center 中，从顶部下拉箭头中选择“群集管理器”。
1. 在“群集连接”下，选择该群集。
1. 在“工具”下，选择“设置” 。
1. 在右窗格中，选择“见证”。
1. 对于 " **见证类型**"，请选择 " **文件共享见证**"。
1. 指定文件共享路径，例如 **\\ servername. com\Witness $** ，并根据需要提供凭据。
1. 单击“保存” 。

## <a name="register-with-azure"></a>注册到 Azure

Azure Stack HCI 需要连接到 Azure，你将需要 Azure Active Directory 权限才能完成注册。 如果尚未安装，请要求 Azure AD 管理员授予权限或将其委派给你。 有关详细信息，请参阅 [将 AZURE STACK HCI 连接到 Azure](register-with-azure.md) 。 注册后，群集会在后台自动进行连接。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Stack HCI 群集并将其注册到 Azure。 你现在已准备好 [创建卷](../manage/create-volumes.md) ，然后 [创建虚拟机](../manage/vm.md)。