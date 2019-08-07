---
title: 在 Azure Stack 上部署以太坊区块链网络 |Microsoft Docs
description: 教程使用自定义解决方案模板来部署和配置 Azure Stack 上的以太坊区块链网络。
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.service: azure-stack
ms.reviewer: seyadava
ms.custom: mvc
manager: femila
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: b68a6df35b5345d3e1f00be126cdae24e87d3d0b
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842986"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack"></a>在 Azure Stack 上部署 Ethereum 区块链网络

Ethereum 解决方案模板旨在利用最少的 Azure 和 Ethereum 知识，使多成员联盟 Ethereum 区块链网络的部署和配置变得更加轻松快捷。

凭借少量的用户输入以及 Azure Stack 租户门户中的一键式部署，每个成员就能预配他们的网络足迹。 每个成员的网络需求量包括三个方面:

1. 一组负载均衡的事务节点, 应用或用户可与这些节点进行交互以提交事务。
2. 用于记录事务的一组挖掘节点。
3. 网络虚拟设备 (NVA)。

稍后连接步骤将连接 Nva 以创建完全配置的多成员区块链网络。

若要设置：

- 选择部署体系结构。
- 部署独立的、协会领导人或联合会成员网络。

## <a name="prerequisites"></a>系统必备

[从市场](../operator/azure-stack-download-azure-marketplace-item.md)下载最新项目：

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- 适用于 Linux 的自定义脚本 2.0
- 适用于 Windows 的自定义脚本扩展

有关区块链方案的详细信息, 请参阅[以太坊证书颁发机构协会解决方案模板](/azure/blockchain/templates/ethereum-poa-deployment)。

## <a name="deployment-architecture"></a>部署体系结构

此解决方案模板可以部署一个或多个成员的以太坊联合会网络。 使用网络虚拟设备和连接资源在链拓扑中连接虚拟网络。

## <a name="deployment-use-cases"></a>部署用例

模板可以通过多种方式为负责人和成员部署以太坊协会。 下面是我们所测试的内容:

- 在具有 Azure AD 或 AD FS 的多节点 Azure Stack 上，使用相同或不同的订阅部署领导者和成员。
- 在单节点 Azure Stack（具有 Azure AD）上，使用相同的订阅部署领导者和成员。

### <a name="standalone-and-consortium-leader-deployment"></a>独立和联盟领导者部署

联盟领导者模板在网络中配置第一个成员的足迹。 

1. [从 GitHub 下载负责人模板](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)。
2. 在 Azure Stack 租户门户中，选择“+ 创建资源”>“模板部署”，以从自定义模板进行部署。
3. 选择“编辑模板”以编辑新的自定义模板。
4. 在右侧的编辑窗格中，复制并粘贴前面下载的领导者模板 JSON。
    
    ![编辑领导者模板](./media/azure-stack-ethereum/edit-leader-template.png)

5. 选择**保存**。
6. 选择“编辑参数”并填写部署的模板参数。
    
    ![编辑领导者模板参数](./media/azure-stack-ethereum/edit-leader-parameters.png)

    参数名称 | 说明 | 允许值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 用作已部署资源的命名基础的字符串。 | 长度为1到6的字母数字字符。 | eth
    AUTHTYPE | 向 VM 进行身份验证的方法。 | 密码或 SSH 公钥。 | 密码
    ADMINUSERNAME | 每个已部署 VM 的管理员用户名。 | 1-64 个字符。 | gethadmin
    ADMINPASSWORD（身份验证类型 = 密码）| 每个部署的 Vm 的管理员帐户的密码。 密码必须包含下列要求中的 3 项：1 个大写字符、1 个小写字符、1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。|12-72 个字符。 |
    ADMINSSHKEY（身份验证类型 = sshPublicKey） | 用于远程登录的安全 shell 密钥。 | |
    GENESISBLOCK | 表示自定义起源块的 JSON 字符串。  此参数的值是可选的。 | |
    ETHEREUMACCOUNTPSSWD | 用于保护以太坊帐户的管理员密码。 | |
    ETHEREUMACCOUNTPASSPHRASE | 用于生成与 Ethereum 帐户相关联的私钥的通行短语。 | |
    ETHEREUMNETWORKID | 联盟的网络 ID。 | 使用5到999999999之间的任何值。 | 72
    CONSORTIUMMEMBERID | 与每个联盟网络成员关联的 ID。   | 此 ID 应在网络中唯一。 | 0
    NUMMININGNODES | 挖掘节点的数量。 | 介于 2 和 15 之间。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点的数量。 | 介于 1 和 5 之间。 | 1
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    BASEURL | 要从中获取部署模板的基 URL。 | 除非想要自定义部署模板，否则请使用默认值。 | 

7. 选择“确定”。
8. 在“自定义部署”中，指定“订阅”、“资源组”和“资源组位置”。
    
    ![领导者部署参数](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    参数名称 | 说明 | 允许值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 要向其部署联合会网络的订阅。 | | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | | EthereumResources
    Location | 资源组的 Azure 区域。 | | 本地

8. 选择“创建”。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后, 请在资源组的 "部署" 部分中查看 " **Microsoft 模板**的部署摘要"。 摘要包含用于联接联盟成员的输出值。

若要验证领导者的部署, 请前往主持人的管理站点。 可在**Microsoft 模板**部署的输出部分找到管理员站点地址。  

![领导者部署摘要](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>加入联盟成员部署

1. [从 GitHub 下载联合会成员模板](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)。
2. 在 Azure Stack 租户门户中，选择“+ 创建资源”>“模板部署”，以从自定义模板进行部署。
3. 选择“编辑模板”以编辑新的自定义模板。
4. 在右侧的编辑窗格中，复制并粘贴前面下载的领导者模板 JSON。
5. 选择**保存**。
6. 选择“编辑参数”并填写部署的模板参数。

    参数名称 | 说明 | 允许值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 用作已部署资源的命名基础的字符串。 | 长度为1到6的字母数字字符。 | eth
    AUTHTYPE | 向 VM 进行身份验证的方法 | 密码或 SSH 公钥。 | 密码
    ADMINUSERNAME | 每个已部署 VM 的管理员用户名 | 1-64 个字符。 | gethadmin
    ADMINPASSWORD（身份验证类型 = 密码）| 每个部署的 Vm 的管理员帐户的密码。 密码必须包含下列要求中的 3 项：1 个大写字符、1 个小写字符、1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。|12-72 个字符。 |
    ADMINSSHKEY（身份验证类型 = sshPublicKey） | 用于远程登录的安全 shell 密钥。 | |
    CONSORTIUMMEMBERID | 与每个联盟网络成员关联的 ID。   | 此 ID 应在网络中唯一。 | 0
    NUMMININGNODES | 挖掘节点的数量。 | 介于 2 和 15 之间。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点的数量。 | 介于 1 和 5 之间。 | 1
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    CONSORTIUMDATA | 指向由其他成员的部署提供的相关联盟配置数据的 URL。 可在领导者的部署输出中找到此值。 | |
    REMOTEMEMBERVNETADDRESSSPACE | 领导者的 NVA IP 地址。 可在领导者的部署输出中找到此值。 | | 
    REMOTEMEMBERNVAPUBLICIP | 领导者的 NVA IP 地址。 可在领导者的部署输出中找到此值。 | | 
    CONNECTIONSHAREDKEY | 联盟网络中正在建立连接的成员之间预先建立的机密。 | |
    BASEURL | 模板的基 URL。 | 除非想要自定义部署模板，否则请使用默认值。 | 

7. 选择“确定”。
8. 在“自定义部署”中，指定“订阅”、“资源组”和“资源组位置”。

    参数名称 | 说明 | 允许值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 要向其部署联合会网络的订阅。 | | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | | MemberResources
    Location | 资源组的 Azure 区域。 | | 本地

8. 选择“创建”。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后, 请在资源组的 "部署" 部分中查看 " **Microsoft 模板**的部署摘要"。 摘要包含用于连接联合会成员的输出值。

若要验证成员的部署，请浏览成员的管理站点。 你可以在**Microsoft 模板**部署的 "输出" 部分找到管理员网站地址。

![成员部署摘要](./media/azure-stack-ethereum/ethereum-node-status-2.png)

如图所示，成员的节点状态为“未运行”。 此状态是因为未建立成员和领导者之间的连接。 成员与领导者之间的连接是双向连接。 部署成员时，模板会自动创建从成员到领导者的连接。 若要创建从领导者到成员的连接，请转到下一步骤。

### <a name="connect-member-and-leader"></a>连接成员和领导者

此模板创建从领导者到远程成员的连接。 

1. [从 GitHub 下载 connect 成员和领导者模板](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)。
2. 在 Azure Stack 租户门户中，选择“+ 创建资源”>“模板部署”，以从自定义模板进行部署。
3. 选择“编辑模板”以编辑新的自定义模板。
4. 在右侧的编辑窗格中，复制并粘贴前面下载的领导者模板 JSON。
    
    ![编辑连接模板](./media/azure-stack-ethereum/edit-connect-template.png)

5. 选择**保存**。
6. 选择“编辑参数”并填写部署的模板参数。
    
    ![编辑连接模板参数](./media/azure-stack-ethereum/edit-connect-parameters.png)

    参数名称 | 说明 | 允许值 | 示例值
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | 领导者的名称前缀。 可在领导者的部署输出中找到此值。  | 长度为1到6的字母数字字符。 | |
    MEMBERROUTETABLENAME | 领导者路由表的名称。 可在领导者的部署输出中找到此值。 |  | 
    REMOTEMEMBERVNETADDRESSSPACE | 成员的地址空间。 可在成员的部署输出中找到此值。 | |
    CONNECTIONSHAREDKEY | 联盟网络中正在建立连接的成员之间预先建立的机密。  | |
    REMOTEMEMBERNVAPUBLICIP | 成员的 NVA IP 地址。 可在成员的部署输出中找到此值。 | |
    MEMBERNVAPRIVATEIP | 领导者的专用 NVA IP 地址。 可在领导者的部署输出中找到此值。 | |
    位置 | Azure Stack 环境的位置。 | | 本地
    BASEURL | 模板的基 URL。 | 除非想要自定义部署模板，否则请使用默认值。 | 

7. 选择“确定”。
8. 在“自定义部署”中，指定“订阅”、“资源组”和“资源组位置”。
    
    ![连接部署参数](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    参数名称 | 说明 | 允许值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 领导者的订阅。 | | 消耗订阅
    资源组 | 领导者的资源组。 | | EthereumResources
    Location | 资源组的 Azure 区域。 | | 本地

8. 选择“创建”。

部署完成后，需要经过几分钟，领导者和成员才会开始通信。 若要验证部署，请刷新成员的管理站点。 成员节点的状态应为“正在运行”。

![验证部署](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>后续步骤

若要详细了解 Ethereum 和 Azure，请参阅[区块链技术与应用程序](https://azure.microsoft.com/solutions/blockchain/)。
