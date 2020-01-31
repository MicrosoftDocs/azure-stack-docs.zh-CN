---
title: 如何通过 VNET 对等互连连接两个 Azure Stack 集线器
description: 了解如何通过 VNET 对等互连连接两个 Azure Stack 集线器。
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 3b257fee978fe576c97855d5af18854082155d7e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884562"
---
# <a name="vnet-peering-in-azure-stack-hub-with-vms"></a>通过 Vm 在 Azure Stack 中心 VNET 对等互连

可以在同一 Azure Stack 中心环境中将两个 Azure Stack 集线器 Vnet 连接到另一个。 目前不能使用内置[虚拟网络网关](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences)连接 Azure Stack 集线器 vnet。 必须使用 NVA 设备在两个 Azure Stack 集线器 Vnet 之间创建 VPN 隧道。 在本文的模板引用中，将部署两个 Windows Server 2016 Vm，并安装 RRAS。 两个 RRAS 服务器都配置为实现两个 Vnet 之间的 S2SVPN IKEv2 隧道。 创建相应的 NSG 和 UDR 规则，以允许在每个 VNET 上指定为**内部**子网之间进行路由。 

此部署模式是一种基础，它不仅允许在 Azure Stack Hub 实例内创建 VPN 隧道，还允许在 Azure Stack 中心实例和其他资源（如使用 Windows RRAS S2S VPN 隧道的本地网络）之间创建 VPN 隧道. 

可以在[Azure 智能边缘模式 GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
)存储库中找到这些模板。 该模板位于**S2SVPNTunnel**文件夹中。

![替换文字](./media/azure-stack-network-howto-vnet-peering/overview.png)

## <a name="requirements"></a>要求

- 应用了最新更新的部署。 
- 必需 Azure Stack 中心市场项：
    -  Windows Server 2016 Datacenter （建议使用最新版本）
    -  自定义脚本扩展

## <a name="things-to-consider"></a>注意事项

- 网络安全组应用于模板隧道子网。 建议使用额外的 NSG 保护每个 VNet 中的内部子网。
- RDP 拒绝规则应用于隧道 NSG，需要将其设置为 "允许" （如果你要通过公共 IP 地址访问 Vm）
- 此解决方案不考虑 DNS 解析
- VNet 名称和 vmName 的组合必须少于15个字符
- 此模板设计用于为 VNet1 和 VNet2 自定义的 VNet 名称
- 此模板正在使用 BYOL windows
- 删除资源组时（1907），必须手动分离隧道子网中的 Nsg，以确保删除资源组完成
- 此模板正在使用 DS3v2 vm。 RRAS 服务将安装并运行 Windows 内部 SQL Server。 如果 VM 大小太小，则可能会导致内存问题。 在减少 VM 大小之前验证性能。
- 这不是一个高度可用的解决方案。 如果需要更高的 HA 样式解决方案，则可以添加另一个 VM，你必须手动将路由表中的路由更改为辅助接口的内部 IP。 还需要配置多个隧道以建立交叉连接。

## <a name="options"></a>选项

- 你可以使用自己的 Blob 存储帐户和 SAS 令牌并使用 _artifactsLocation 和 _artifactsLocationSasToken 参数
- 此模板 INTERNALSUBNETREFVNET1 和 INTERNALSUBNETREFVNET2 上有两个输出，这是内部子网的资源 Id （如果想要在管道样式部署模式中使用它）。

模板为 VNet 命名和 IP 寻址提供默认值。 它需要管理员的密码（rrasadmin），并且还可以将自己的存储 blob 与 SAS 令牌配合使用。 请注意，在合法范围内保留这些值，因为部署可能会失败。 将在每个 RRAS VM 上执行 PowerShell DSC 包，并安装路由和所有必需的从属服务和功能。 如果需要，可以进一步自定义此 DSC。 自定义脚本扩展运行以下脚本，并 `Add-Site2Site.ps1` 使用共享密钥在两个 RRAS 服务器之间配置 VPNS2S 隧道。 可以查看自定义脚本扩展的详细输出，以查看 VPN 隧道配置的结果

![替换文字](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.png)

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)  