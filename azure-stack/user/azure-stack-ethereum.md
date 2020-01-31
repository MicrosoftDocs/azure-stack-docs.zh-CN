---
title: 在 Azure Stack 集线器上部署以太坊区块链网络
description: 教程使用自定义解决方案模板在 Azure Stack 集线器上部署和配置联合会以太坊区块链网络。
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: c981fb453389c4e088db94474dd757f7881dfc6b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883694"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>在 Azure Stack 集线器上部署以太坊区块链网络

以太坊解决方案模板旨在使使用最小的 Azure 和以太坊知识更轻松快捷地部署和配置多成员协会以太坊区块链网络。

通过 Azure Stack 中心租户门户提供少量用户输入和单击部署，每个成员都可以设置其网络空间。 每个成员的网络需求量包括三个方面：

1. 一组负载均衡的事务节点，应用或用户可与这些节点进行交互以提交事务。
2. 用于记录事务的一组挖掘节点。
3. 网络虚拟设备（NVA）。

稍后连接步骤将连接 Nva 以创建完全配置的多成员区块链网络。

若要设置：

- 选择部署体系结构。
- 部署独立的、协会领导人或联合会成员网络。

## <a name="prerequisites"></a>必备组件

[从 Marketplace](../operator/azure-stack-download-azure-marketplace-item.md)下载最新项目：

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- 适用于 Linux 2.0 的自定义脚本
- 适用于 Windows 的自定义脚本扩展

有关区块链方案的详细信息，请参阅[以太坊证书颁发机构协会解决方案模板](/azure/blockchain/templates/ethereum-poa-deployment)。

## <a name="deployment-architecture"></a>部署体系结构

此解决方案模板可以部署一个或多个成员的以太坊联合会网络。 虚拟网络在链拓扑中使用网络虚拟设备和连接资源进行连接。

## <a name="deployment-use-cases"></a>部署用例

模板可以通过多种方式为负责人和成员部署以太坊协会。 下面是我们所测试的内容：

- 在具有 Azure AD 或 AD FS 的多节点 Azure Stack 集线器上，使用同一订阅或不同订阅部署潜在客户和成员。
- 在单节点 Azure Stack 集线器（使用 Azure AD）上，使用同一订阅部署潜在客户和成员。

### <a name="standalone-and-consortium-leader-deployment"></a>独立和联盟领导部署

协会负责人模板配置网络中第一个成员的占用量。 

1. [从 GitHub 下载负责人模板](https://aka.ms/aa6z619)。
2. 在 Azure Stack 中心租户门户中，选择 " **+ 创建资源" > 模板部署**从自定义模板部署。
3. 选择 "**编辑模板**" 以编辑新的自定义模板。
4. 在右侧的编辑窗格中，复制并粘贴之前下载的引线模板 JSON。
    
    ![编辑引线模板](./media/azure-stack-ethereum/edit-leader-template.png)

5. 选择“保存”。
6. 选择 "**编辑参数**" 并完成部署的模板参数。
    
    ![编辑引线模板参数](./media/azure-stack-ethereum/edit-leader-parameters.png)

    参数名 | Description | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 用作命名已部署资源的基础的字符串。 | 长度为1到6的字母数字字符。 | eth
    AUTHTYPE | 向 VM 进行身份验证的方法。 | 密码或 SSH 公钥。 | 密码
    ADMINUSERNAME | 每个已部署 VM 的管理员用户名。 | 1-64 个字符。 | gethadmin
    ADMINPASSWORD （身份验证类型 = 密码）| 每个部署的 Vm 的管理员帐户的密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。|12-72 个字符。 |
    ADMINSSHKEY （身份验证类型 = sshPublicKey） | 用于远程登录的安全 shell 密钥。 | |
    GENESISBLOCK | 表示自定义起源块的 JSON 字符串。  为此参数指定值是可选的。 | |
    ETHEREUMACCOUNTPSSWD | 用于保护以太坊帐户的管理员密码。 | |
    ETHEREUMACCOUNTPASSPHRASE | 用于生成与以太坊帐户关联的私钥的通行短语。 | |
    ETHEREUMNETWORKID | 联盟的网络 ID。 | 使用5到999999999之间的任何值。 | 72
    CONSORTIUMMEMBERID | 与联盟网络的每个成员关联的 ID。   | 此 ID 在网络中应该是唯一的。 | 0
    NUMMININGNODES | 挖掘节点数。 | 介于2到15之间。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点数。 | 介于1和5之间。 | 第
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    BASEURL | 要从中获取部署模板的基 URL。 | 除非要自定义部署模板，否则请使用默认值。 | 

7. 选择“确定”。
8. 在 "**自定义部署**" 中，指定**订阅**、**资源组**和**资源组位置**。
    
    ![领导者部署参数](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    参数名 | Description | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 要向其部署联合会网络的订阅。 | | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | | EthereumResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择“创建”。

部署可能需要20分钟或更长时间才能完成。

部署完成后，请在资源组的 "部署" 部分中查看 " **Microsoft 模板**的部署摘要"。 摘要包含用于联接联盟成员的输出值。

若要验证领导者的部署，请前往主持人的管理站点。 可在**Microsoft 模板**部署的输出部分找到管理员站点地址。  

![领导者部署摘要](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>加入联合会成员部署

1. [从 GitHub 下载联合会成员模板](https://aka.ms/aa6zkua)。
2. 在 Azure Stack 中心租户门户中，选择 " **+ 创建资源" > 模板部署**从自定义模板部署。
3. 选择 "**编辑模板**" 以编辑新的自定义模板。
4. 在右侧的编辑窗格中，复制并粘贴你先前下载的引线模板 JSON。
5. 选择“保存”。
6. 选择 "**编辑参数**" 并完成部署的模板参数。

    参数名 | Description | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 用作命名已部署资源的基础的字符串。 | 长度为1到6的字母数字字符。 | eth
    AUTHTYPE | 向 VM 进行身份验证的方法 | 密码或 SSH 公钥。 | 密码
    ADMINUSERNAME | 每个已部署 VM 的管理员用户名 | 1-64 个字符。 | gethadmin
    ADMINPASSWORD （身份验证类型 = 密码）| 每个部署的 Vm 的管理员帐户的密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。|12-72 个字符。 |
    ADMINSSHKEY （身份验证类型 = sshPublicKey） | 用于远程登录的安全 shell 密钥。 | |
    CONSORTIUMMEMBERID | 与联盟网络的每个成员关联的 ID。   | 此 ID 在网络中应该是唯一的。 | 0
    NUMMININGNODES | 挖掘节点数。 | 介于2到15之间。 | 2
    MNNODEVMSIZE | 挖掘节点的 VM 大小。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 挖掘节点的存储性能。 | | Standard_LRS
    NUMTXNODES | 事务节点数。 | 介于1和5之间。 | 第
    TXNODEVMSIZE | 事务节点的 VM 大小。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 事务节点的存储性能。 | | Standard_LRS
    CONSORTIUMDATA | 指向由其他成员的部署提供的相关联盟配置数据的 URL。 此值可在领导者的部署输出中找到。 | |
    REMOTEMEMBERVNETADDRESSSPACE | 领导者的 NVA IP 地址。 此值可在领导者的部署输出中找到。 | | 
    REMOTEMEMBERNVAPUBLICIP | 领导者的 NVA IP 地址。 此值可在领导者的部署输出中找到。 | | 
    CONNECTIONSHAREDKEY | 建立连接的联合会网络成员之间的预先建立的机密。 | |
    BASEURL | 模板的基 URL。 | 除非要自定义部署模板，否则请使用默认值。 | 

7. 选择“确定”。
8. 在 "**自定义部署**" 中，指定**订阅**、**资源组**和**资源组位置**。

    参数名 | Description | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 要向其部署联合会网络的订阅。 | | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | | MemberResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择“创建”。

部署可能需要20分钟或更长时间才能完成。

部署完成后，请在资源组的 "部署" 部分中查看 " **Microsoft 模板**的部署摘要"。 摘要包含用于连接联合会成员的输出值。

若要验证成员的部署，请浏览成员的管理站点。 你可以在**Microsoft 模板**部署的 "输出" 部分找到管理员网站地址。

![成员部署摘要](./media/azure-stack-ethereum/ethereum-node-status-2.png)

如图所示，成员的节点状态不是 "**正在运行**"。 此状态是因为未建立成员和领导者之间的连接。 成员与引线之间的连接是双向连接。 部署成员时，模板会自动创建从成员到领导的连接。 若要创建从负责人到成员的连接，请执行下一步。

### <a name="connect-member-and-leader"></a>连接成员和负责人

此模板创建从引线到远程成员的连接。 

1. [从 GitHub 下载 connect 成员和领导者模板](https://aka.ms/aa6zdyt)。
2. 在 Azure Stack 中心租户门户中，选择 " **+ 创建资源" > 模板部署**从自定义模板部署。
3. 选择 "**编辑模板**" 以编辑新的自定义模板。
4. 在右侧的编辑窗格中，复制并粘贴你先前下载的引线模板 JSON。
    
    ![编辑连接模板](./media/azure-stack-ethereum/edit-connect-template.png)

5. 选择“保存”。
6. 选择 "**编辑参数**" 并完成部署的模板参数。
    
    ![编辑连接模板参数](./media/azure-stack-ethereum/edit-connect-parameters.png)

    参数名 | Description | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | 领导者的名称前缀。 此值可在领导者的部署输出中找到。  | 长度为1到6的字母数字字符。 | |
    MEMBERROUTETABLENAME | 负责人的路由表的名称。 此值可在领导者的部署输出中找到。 |  | 
    REMOTEMEMBERVNETADDRESSSPACE | 成员的地址空间。 此值可在成员的部署输出中找到。 | |
    CONNECTIONSHAREDKEY | 建立连接的联合会网络成员之间的预先建立的机密。  | |
    REMOTEMEMBERNVAPUBLICIP | 成员的 NVA IP 地址。 此值可在成员的部署输出中找到。 | |
    MEMBERNVAPRIVATEIP | 领导者的私有 NVA IP 地址。 此值可在领导者的部署输出中找到。 | |
    位置 | Azure Stack 中心环境的位置。 | | local
    BASEURL | 模板的基 URL。 | 除非要自定义部署模板，否则请使用默认值。 | 

7. 选择“确定”。
8. 在 "**自定义部署**" 中，指定**订阅**、**资源组**和**资源组位置**。
    
    ![连接部署参数](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    参数名 | Description | 允许的值 | 示例值
    ---------------|-------------|----------------|-------------
    订阅 | 领导者的订阅。 | | 消耗订阅
    资源组 | 领导者的资源组。 | | EthereumResources
    位置 | 资源组的 Azure 区域。 | | local

8. 选择“创建”。

部署完成后，负责人和成员开始通信需要几分钟时间。 若要验证部署，请刷新成员的管理站点。 成员节点的状态应为 "正在运行"。

![验证部署](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>后续步骤

若要了解有关以太坊和 Azure 的详细信息，请参阅[区块链技术和应用程序](https://azure.microsoft.com/solutions/blockchain/)。
