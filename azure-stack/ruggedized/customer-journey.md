---
title: 客户从购买阶段到 Azure Stack Hub 部署后阶段的过程 | Microsoft Docs
description: 从规划到后期部署，了解从 Microsoft 成功部署 Azure Stack 中心耐用所期望的内容。
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: f34a449490ab7b57beacc942584f9616fd6d543c
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598311"
---
# <a name="customer-journey"></a>客户旅程

本文介绍了从购买到后期部署 Azure Stack 中心数据中心集成的端到端过程。 集成是客户与 Microsoft 之间的协作项目。 以下各部分介绍项目时间线上的不同阶段和项目成员要完成的具体步骤。

## <a name="introduction"></a>介绍

下表描述了各部署阶段的预期步骤。

|   |订单处理  |部署前 |集成、验证、传输 |现场部署  |部署之后 |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
| **Microsoft** |-发送到美国位置的信号<br>-Azure Stack 中心耐用 = 10 天 |提供收集数据中心要求所需的工具和文档  |- 验证配置项目并查看验证结果<br>- 确保交付硬件  |- 机架和堆栈<br>- 网络集成<br>- Azure Stack Hub 部署<br>- 移交给客户    |注册和市场联合|
| **客户** |信号购买    |- 在部署工作表中填写网络详细信息<br>- 收集证书<br>- 获取 Azure AD 帐户<br>- 运行提供的任何验证工具    |确保站点已满足网络、电源、冷却先决条件    |- 已准备好部署配置项目<br>- 客户的网络工程师已准备好提供服务   |     |


## <a name="order-process"></a>订单处理

你的组织将与 Microsoft 合作，以便为分配的系统数设置订单。 订购后，Microsoft 将有10天的时间将 Azure Stack 中心耐用到美国的位置。 Microsoft 将确保满足所有安全供应链要求。 

>[!NOTE] 
>计费在硬件交付后的14天内开始。


若要创建 Azure Stack 集线器资源，请在 Azure 门户中执行以下步骤。

1. 使用 Microsoft Azure 凭据通过以下 URL 登录到 Azure 门户：[https://portal.azure.com](https://portal.azure.com)。
1. 在左窗格中，选择“+ 创建资源”。 搜索并选择 **Azure Stack 中心耐用**。 选择“创建”  。
1. 选择要用于 Azure Stack 中心设备的订阅。 选择要将此物理设备寄送到的国家/地区。 选择“显示设备”。
1. 此处将显示缩写形式。 填写表格，并选择“提交”。 Microsoft 将启用你的订阅。
1. 启用订阅后，你应该能继续创建资源。 在“选择设备类型”边栏选项卡中，选择“选择” 。 
1. 在“基本信息”选项卡上，输入或选择以下“项目详细信息”。  
    
    |设置  |值  |
    |---------|---------|
    |订阅    |系统会根据前面所做的选择自动填充此字段。 订阅将链接到你的计费帐户。 |
    |资源组  |选择现有的组，或创建新组。   |

1. 输入或选择以下“实例详细信息”。 

    |设置  |值  |
    |---------|---------|
    |名称   | 用于标识资源的友好名称。<br>该名称的长度必须介于 2 和 50 个字符之间，只能包含字母、数字和连字符。<br> 名称以字母或数字开头和结尾。        |
    |区域     |有关 Azure Stack 中心资源可用的所有区域的列表，请参阅 [按区域提供的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure 政府版，则可选择 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中显示的所有可用的政府区域。<br> 选择离要部署设备的地理区域最近的位置。|


1. 在完成时选择“下一步:送货地址”。

    - 如果已经有一个设备，请选择组合框，以使 **其具有 Azure Stack 集线器设备**。

    - 如果这是你订购的新设备，请输入联系人姓名、公司、寄送设备的地址，以及联系人信息。

1. 在完成时选择“下一步:查看 + 创建”。
1. 在“查看 + 创建”选项卡上，查看“定价详细信息”、“使用条款”和资源的详细信息。 选择与“我已经查看隐私条款”对应的组合框。
1. 选择“创建”。

创建资源需要几分钟时间。 成功创建并部署资源后，你会收到通知。 选择“转到资源”。 

在你下单以后，Microsoft 会审核订单并通过电子邮件联系你，核对配送详细信息。

## <a name="pre-deployment"></a>部署前

决定如何将 Azure Stack Hub 集成到数据中心。 Microsoft 发布了[部署工作表](../operator/azure-stack-deployment-worksheet.md)，可指导你收集成功集成到数据中心所需的所有必要信息。 此外，在部署时需要一组特定的证书。 为了帮助你获取这些证书，Microsoft 为你提供了一个称为 [Azure Stack 中心就绪检查](../operator/azure-stack-validation-report.md)程序的工具。 你可以借助此工具创建要向内部 CA 提供的证书签名请求 (CSR)。 

>[!Important]
>验证所有先决条件，以帮助防止部署延迟。 验证先决条件可能需要一些时间，并且需要组织中的不同部门进行协调和数据收集。

需选择以下项：

- Azure Stack Hub 连接模型和标识提供者。 可以选择在[已连接到 Internet（和 Azure）](../operator/azure-stack-connected-deployment.md)时或[断开连接](../operator/azure-stack-disconnected-deployment.md)时部署 Azure Stack Hub。 若要从 Azure Stack Hub（包括混合方案）获得最大效益，建议在连接到 Azure 时进行部署。 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 以后，除非重新部署整个系统，否则将无法更改标识提供者。
- 网络集成。 [网络集成](../operator/azure-stack-network.md)对于部署、操作和管理 Azure Stack Hub 系统至关重要。 需要考虑到多种因素才能确保 Azure Stack Hub 解决方案具有复原能力，并提供一个高可用性物理基础设施来支持其操作。
- 防火墙集成。 建议[使用防火墙](../operator/azure-stack-firewall.md)来帮助保护 Azure Stack Hub。 防火墙有助于防止 DDOS 攻击，以及执行入侵检测和内容检查。 但应注意，它可能成为 Azure 存储服务的吞吐量瓶颈。
- 证书要求。 必须在现场工程师抵达数据中心进行部署之前准备好全部[所需的证书](../operator/azure-stack-pki-certs.md)。

通过 "部署" 工作表收集所有先决条件信息后，Microsoft 将确保验证所有验证工具是否已运行，并协助您可能遇到的任何问题。 

## <a name="hardware-delivery"></a>硬件交付

Microsoft 将与你合作，以确保所有必需的硬件在给定的分配时间内到达美国位置。  

**重要** 的是，所有先决条件数据都已锁定，并 *在现场 Microsoft 部署工程师到达部署解决方案之前可用。*

- 部署工作表上已填写所有数据。 
- 必须验证并准备好所有证书。
- 必须确定区域名。
- 已确定和完成所有网络集成参数。

>[!Tip]
>如果上述任何信息发生了更改，请务必与内部组织合作，确保在现场部署工程师到达之前更新信息。 这将防止部署过程发生延迟。

## <a name="onsite-deployment"></a>现场部署

若要部署 Azure Stack 中心，可以使用 Microsoft 部署工程师来启动部署。 我们要求在现场部署期间，你组织有随时待命的网络工程师。

以下检查是部署体验期间现场工程师应执行的操作：

- 硬件的取消装箱和盘存
- 连接电源并为解决方案接通电源
- 验证物理硬件运行状况
- 检查所有布线和边界连接，确保解决方案正确定位在一起，且符合要求。
- 配置解决方案 HLH (硬件生命周期主机) 。
- 数据中心网络集成
- 检查以确保所有物理硬件设置都正确。
- 确保所有组件的固件使用解决方案批准的最新版本。
- 开始部署。 

## <a name="post-deployment"></a>后期部署

在将解决方案移交给客户之前，必须先执行几个步骤。 在此阶段，验证非常重要，它可以确保系统部署正确且运行正常。

Microsoft 部署工程师应执行的操作：

- 启用值-将资源提供程序 (RPs) 。
- 运行 [test-azurestack](../operator/azure-stack-diagnostic-test.md)。
- [注册](../operator/azure-stack-registration-role.md) 到 Azure。
- [Marketplace 联合](../operator/azure-stack-marketplace.md)。
- 备份交换机配置和 HLH 配置文件。
- 为客户准备部署摘要。
- [检查更新](../operator/azure-stack-updates.md) ，确保解决方案软件已更新到最新版本。

## <a name="next-steps"></a>后续步骤

详细了解 [安装和配置 Microsoft Azure Stack 集线器坚固的步骤](deployment-overview.md)。

