---
title: 在 Azure Stack 集线器上部署以太坊区块链网络
description: 教程：使用自定义解决方案模板在 Azure Stack Hub 上部署和配置联盟 Ethereum 区块链网络。
author: PatAltimore
ms.author: patricka
ms.date: 10/07/2020
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 10/07/2020
ms.openlocfilehash: f409a2629243c0eb1cfe15ad857aa3e229832bb5
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493693"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>在 Azure Stack Hub 上部署 Ethereum 区块链网络

Ethereum 解决方案模板旨在利用最少的 Azure 和 Ethereum 知识，使多成员联盟 Ethereum 区块链网络的部署和配置变得更加轻松快捷。

凭借少量的用户输入以及 Azure Stack Hub 租户门户中的一键式部署，每个成员都能预配其内存占用情况。 每个成员的网络足迹由三部分组成：

1. 一组负载均衡的事务节点，应用或用户可与这些节点进行交互以提交事务。
2. 一组用于记录事务的挖掘节点。
3. 一个网络虚拟设备 (NVA)。

稍后的连接步骤将连接 NVA 以创建完全配置的多成员区块链网络。

若要设置：

- 选择部署体系结构。
- 部署独立网络、联盟领导者网络或联盟成员网络。

## <a name="prerequisites"></a>必备条件

[从市场](../operator/azure-stack-download-azure-marketplace-item.md)下载最新项目：

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- 适用于 Linux 的自定义脚本 2.0
- 适用于 Windows 的自定义脚本扩展

有关区块链方案的更多信息，请参阅 [Ethereum 权威证明联盟解决方案模板](/azure/blockchain/templates/ethereum-poa-deployment)。

## <a name="deployment-architecture"></a>部署体系结构

此解决方案模板可以部署单成员或多成员 Ethereum 联盟网络。 使用网络虚拟设备和连接资源在链拓扑中连接虚拟网络。

此模板可以通过多种方式为领导者和成员部署 Ethereum 联盟。 下面是我们已测试过的内容：

- 在具有 Azure AD 或 AD FS 的多节点 Azure Stack 集线器上，使用同一订阅或不同订阅部署潜在客户和成员。
- 在具有 Azure AD) 的单节点 Azure Stack 中心 (上，使用同一订阅部署潜在客户和成员。

## <a name="standalone-and-consortium-leader-deployment"></a>独立和联盟领导者部署

联盟领导者模板在网络中配置第一个成员的足迹。 

1. [从 GitHub 下载领导者模板](https://aka.ms/aa6z619)。
1. 在 Azure Stack 中心租户门户中，选择 " **+ 创建资源" > 模板部署** 从自定义模板部署。
1. 选择 **"在编辑器中生成自己的模板** " 以编辑新的自定义模板。
1. 在右侧的编辑窗格中，复制并粘贴前面下载的领导者模板 JSON。
    
    [![粘贴了前导模板的编辑模板](./media/azure-stack-ethereum/edit-leader-template.png)](./media/azure-stack-ethereum/edit-leader-template.png#lightbox)

1. 选择“保存”。
1. 在 " **基本** 信息" 选项卡上，完成以下设置。

    参数名称 | 说明 | 示例值
    ---------------|-------------|-------------
    订阅 | 要将联盟网络部署到的订阅。 | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | EthereumResources
    区域 | 用于资源的 Azure 区域。 | local
    名称前缀 | 用作已部署资源的命名基础的字符串。 最多使用六个字母数字字符。 | eth
    身份验证类型 | 向 VM 进行身份验证的方法。 允许的值为密码或 SSH 公钥。 | 密码
    管理员用户名 | 部署的每个 VM 的管理员用户名。 使用1到64个字符。 | gethadmin
    管理员密码 (身份验证类型 = 密码) | 部署的每个 VM 的管理员帐户密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。 使用12到72个字符。 |
    管理员 SSH 密钥 (Authentication type = sshPublicKey)  | 安全外壳 RSA 用于远程登录的公钥字符串。 |
    Genesis 块 | 表示自定义起源块的 JSON 字符串。  此参数的值是可选的。 |
    以太坊帐户密码 | 用于保护 Ethereum 帐户的管理员密码。 |
    以太坊帐户密码 | 用于生成与 Ethereum 帐户相关联的私钥的通行短语。 请考虑具有足够随机性的密码，以确保强私钥。 |
    以太坊网络 ID | 联盟的网络 ID。 使用介于 5 和 999,999,999 之间的任何值。 | 72
    联合会成员 ID | 与每个联盟网络成员关联的 ID。 此 ID 应在网络中唯一。 | 0
    挖掘节点数量 | 每个联合会成员的挖掘节点数。 使用2到15之间的值。 | 2
    挖掘节点 VM 大小 | 挖掘节点的 VM 大小。 | Standard_A1
    挖掘存储帐户类型 | 挖掘节点的存储性能。 | Standard_LRS
    数字 TX 节点 | 负载均衡的事务节点数。 使用介于1和5之间的值。 | 1
    TX 节点 VM 大小 | 事务节点的 VM 大小。 | Standard_A1
    TX 存储帐户类型 | 事务节点的存储性能。 | Standard_LRS
    基 URL | 用于获取部署模板的基 URL。 除非想要自定义部署模板，否则请使用默认值。 |

1. 选择“查看 + 创建”。 验证成功后，选择 " **创建** "。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后，请在资源组的部署部分中查看 **Microsoft.Template** 的部署摘要。 摘要中包含用于加入联盟成员的输出值。

若要验证领导者的部署，请转到领导者的管理站点。 可在 **Microsoft.Template** 部署的输出部分找到管理站点地址。  

![领导者部署摘要](./media/azure-stack-ethereum/ethereum-node-status.png)

## <a name="joining-consortium-member-deployment"></a>加入联盟成员部署

1. [从 GitHub 下载联盟成员模板](https://aka.ms/aa6zkua)。
1. 在 Azure Stack 中心租户门户中，选择 " **+ 创建资源" > 模板部署** 从自定义模板部署。
1. 选择 **"在编辑器中生成自己的模板** " 以编辑新的自定义模板。
1. 在右侧的编辑窗格中，复制并粘贴之前下载的 "联盟" 成员模板 JSON。
1. 选择“保存”。
1. 在 " **基本** 信息" 选项卡上，完成以下设置。

    参数名称 | 说明 | 示例值
    ---------------|-------------|-------------
    订阅 | 要将联盟网络部署到的订阅。 | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | EthereumResources
    区域 | 用于资源的 Azure 区域。 | local
    名称前缀 | 用作已部署资源的命名基础的字符串。 最多使用六个字母数字字符。 | eth
    身份验证类型 | 向 VM 进行身份验证的方法。 允许的值为密码或 SSH 公钥。 | 密码
    管理员用户名 | 部署的每个 VM 的管理员用户名。 使用1到64个字符。 | gethadmin
    管理员密码 (身份验证类型 = 密码) | 部署的每个 VM 的管理员帐户密码。 密码必须包含下列要求中的 3 项: 1 个大写字符，1 个小写字符，1 个数字和 1 个特殊字符。 <br />虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。 使用12到72个字符。 |
    管理员 SSH 密钥 (Authentication type = sshPublicKey)  | 安全外壳 RSA 用于远程登录的公钥字符串。 |
    Genesis 块 | 表示自定义起源块的 JSON 字符串。 此参数的值是可选的。 |
    以太坊帐户密码 | 用于保护 Ethereum 帐户的管理员密码。 |
    以太坊帐户密码 | 用于生成与 Ethereum 帐户相关联的私钥的通行短语。 请考虑具有足够随机性的密码，以确保强私钥。 |
    联合会成员 ID | 与每个联盟网络成员关联的 ID。 此 ID 应在网络中唯一。 | 0
    挖掘节点数量 | 每个联合会成员的挖掘节点数。 使用2到15之间的值。 | 2
    挖掘节点 VM 大小 | 挖掘节点的 VM 大小。 | Standard_A1
    挖掘存储帐户类型 | 挖掘节点的存储性能。 | Standard_LRS
    数字 TX 节点 | 负载均衡的事务节点数。 使用介于1和5之间的值。 | 1
    TX 节点 VM 大小 | 事务节点的 VM 大小。 | Standard_A1
    TX 存储帐户类型 | 事务节点的存储性能。 | Standard_LRS
    联盟数据 | 指向由其他成员的部署提供的相关联盟配置数据的 URL。 可在领导者的部署输出中找到此值。 |
    远程成员 VNET 地址空间 | 领导者的 VNET 地址空间。 可在领导者的部署输出中找到此值。 |
    远程成员 NVA 公共 IP | 领导者的 NVA IP 地址。 可在领导者的部署输出中找到此值。 |
    连接共享密钥 | 在建立网关连接的联合会网络成员之间预建立的机密。 |
    基 URL | 用于获取部署模板的基 URL。 除非想要自定义部署模板，否则请使用默认值。 |
1. 选择“查看 + 创建”。 验证成功后，选择 " **创建** "。

部署可能需要 20 分钟或更长时间才能完成。

部署完成后，请在资源组的部署部分中查看 **Microsoft.Template** 的部署摘要。 摘要中包含用于连接联盟成员的输出值。

若要验证成员的部署，请浏览成员的管理站点。 可在 **Microsoft.Template** 部署的输出部分找到管理站点地址。

![成员部署摘要](./media/azure-stack-ethereum/ethereum-node-status-2.png)

如图所示，成员的节点状态为“未运行”。 之所以出现此状态是因为成员与领导者之间未建立连接。 成员与领导者之间的连接是双向连接。 部署成员时，模板会自动创建从成员到领导者的连接。 若要创建从领导者到成员的连接，请转到下一步骤。

## <a name="connect-member-and-leader"></a>连接成员和领导者

此模板创建从领导者到远程成员的连接。 

1. [从 GitHub 下载连接成员和领导者的模板](https://aka.ms/aa6zdyt)。
1. 在 Azure Stack 中心租户门户中，选择 " **+ 创建资源" > 模板部署** 从自定义模板部署。
1. 选择 **"在编辑器中生成自己的模板** " 以编辑新的自定义模板。
1. 在右侧的编辑窗格中，复制并粘贴之前下载的 "联盟" 成员模板 JSON。
1. 选择“保存”。
1. 完成以下设置。

    参数名称 | 说明 | 示例值
    ---------------|-------------|-------------
    订阅 | 要将联盟网络部署到的订阅。 | 消耗订阅
    资源组 | 部署联盟网络的资源组。 | EthereumResources
    区域 | 用于资源的 Azure 区域。 | local
    成员名称前缀 | 用作已部署资源的命名基础的字符串。 最多使用六个字母数字字符。 | eth
    成员路由表名称 | 领导者路由表的名称。 可在领导者的部署输出中找到此值。 |
    远程成员 VNET 地址空间 | 成员的地址空间。 可在成员的部署输出中找到此值。 |
    远程成员 NVA 公共 IP | 要连接到的 NVA IP 地址。 可在成员的部署输出中找到此值。 |
    连接共享密钥 | 联盟网络中正在建立连接的成员之间预先建立的机密。  |
    成员 NVA 专用 IP | 成员的 NVA IP 地址。 可在成员的部署输出中找到此值。 |
    基 URL | 用于获取部署模板的基 URL。 除非想要自定义部署模板，否则请使用默认值。 |
1. 选择“查看 + 创建”。 验证成功后，选择 " **创建** "。

部署完成后，需要经过几分钟，领导者和成员才会开始通信。 若要验证部署，请刷新成员的管理站点。 成员节点的状态应为“正在运行”。

![验证部署](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>后续步骤

若要详细了解 Ethereum 和 Azure，请参阅[区块链技术与应用程序](https://azure.microsoft.com/solutions/blockchain/)。
