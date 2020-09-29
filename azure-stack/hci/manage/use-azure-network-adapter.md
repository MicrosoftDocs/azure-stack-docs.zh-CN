---
title: 将服务器连接到 Azure 虚拟网络-Azure 网络适配器
description: 本文提供了有关如何使用 Azure 网络适配器将服务器连接到 Azure 虚拟网络的要求和步骤。
ms.topic: article
author: thomasmaurer
ms.author: thmaure
ms.date: 07/14/2020
ms.openlocfilehash: 523ee87a5673a79dbb17c18251fc8837b9d2f7e4
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573643"
---
# <a name="use-azure-network-adapter-to-connect-a-server-to-an-azure-virtual-network"></a>使用 Azure 网络适配器将服务器连接到 Azure 虚拟网络

>适用于：Windows Server 2019、Windows Server 2016、Windows Server 2012 R2

许多在本地和多云环境中运行的工作负荷需要连接到 (Vm) 在 Microsoft Azure 中运行的虚拟机。 若要将服务器连接到 Azure 虚拟网络，可以使用多个选项，包括站点到站点 VPN、Azure Express Route 和点到站点 VPN。

Windows Admin Center 和 Azure 网络适配器提供一键式体验，以使用[点到站点 VPN](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) 连接将服务器连接到虚拟网络。 此过程会自动配置虚拟网络网关和本地 VPN 客户端。

## <a name="when-to-use-azure-network-adapter"></a>何时使用 Azure 网络适配器
如果要从远程位置（例如，分支机构、商店或其他位置）连接到虚拟网络，Azure 网络适配器点到站点 VPN 连接会很有用。 如果只需要将少量服务器连接到虚拟网络，则还可以使用 Azure 网络适配器而不是站点到站点 VPN。 Azure 网络适配器连接不需要 VPN 设备或面向公众的 IP 地址。

### <a name="requirements"></a>要求
使用 Azure 网络适配器连接到虚拟网络需要满足以下条件：
- 具有至少一个有效订阅的 Azure 帐户。
- 现有虚拟网络。
- 对要连接到 Azure 虚拟网络的目标服务器的 Internet 访问权限。
- Windows Admin Center 到 Azure 的连接。
  若要了解详细信息，请参阅[配置 Azure 集成](/windows-server/manage/windows-admin-center/azure/azure-integration)。
- 最新版本的 Windows Admin Center。
  若要了解详细信息，请参阅 [Windows Admin Center](https://www.microsoft.com/windows-server/windows-admin-center)。

> [!NOTE]
> 不需要在要连接到 Azure 的服务器上安装 Windows 管理中心。 但可以在单个服务器方案中执行该操作。

## <a name="add-an-azure-network-adapter-to-a-server"></a>向服务器添加 Azure 网络适配器
若要配置 Azure 网络适配器，请在 Windows Admin Center 中转到其网络扩展。

在 Windows Admin Center 中，请执行以下操作：
1. 导航到承载要添加到 Azure 网络适配器的 VM 的服务器。
1. 在“工具”下，选择“网络”。
1. 选择“添加 Azure 网络适配器”。
1. 在“添加 Azure 网络适配器”窗格上，输入以下所需信息，然后选择“创建” ：
    - **订阅**
    - **位置**
    - **虚拟网络**
    - 如果不存在**网关子网** () 
    - 如果不存在**网关 SKU** () 
    - **客户端地址空间**

        客户端地址池是指定的专用 IP 地址的范围。 通过点到站点 VPN 进行连接的客户端动态接收此范围内的 IP 地址。 使用专用 IP 地址范围时，该范围不得与要由其进行连接的本地位置重叠，也不得与要连接到的虚拟网络重叠。 建议使用为专用网络指定的范围内的 IP 地址（10.x.x.x、192.168.x.x 或 172.16.0.0 到 172.31.255.255）。

    - **身份验证证书**

        Azure 使用证书对通过点到站点 VPN 连接连接到虚拟网络的客户端进行身份验证。 将根证书的公钥信息上传到 Azure， 然后，Azure 会将根证书视为 "受信任"，以使其与虚拟网络建立点到站点连接。 客户端证书必须从受信任的根证书生成，并安装在客户端服务器上。 当客户端发起与虚拟网络的连接时，需使用客户端证书对客户端进行身份验证。
    
        若要了解详细信息，请参阅 [使用本机 Azure 证书身份验证配置与 VNet 的点到站点 VPN 连接： Azure 门户](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)中的 "配置身份验证类型" 部分。

    :::image type="content" source="media/azure-network-adapter/add-azure-network-adapter.png" alt-text="Windows Admin Center 中的“添加 Azure 网络适配器”窗格。":::

> [!NOTE]
> 在虚拟网络中运行的 VPN 网关和应用程序网关等网络设备附带额外的费用。 若要了解详细信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。

如果没有现有的 Azure 虚拟网络网关，则 Windows Admin Center 会为你创建一个。 此设置过程最多可能需要 25 分钟。 创建 Azure 网络适配器后，可以开始直接从服务器访问虚拟网络中的 VM。

如果不再需要连接，请在 " **网络**" 下，选择要断开连接的 Azure 网络适配器，从顶部菜单中选择 " **断开连接**"，然后在 " **断开连接 VPN 确认** " 弹出窗口中选择 **"是"**。

## <a name="next-steps"></a>后续步骤
有关 Azure 虚拟网络的详细信息，另请参阅：

- [Azure 虚拟网络常见问题解答 (FAQ)](/azure/virtual-network/virtual-networks-faq)
