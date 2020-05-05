---
title: 如何通过 VNET 对等互连连接两个 Azure Stack Hub
description: 了解如何通过 VNET 对等互连连接两个 Azure Stack Hub。
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 97fc89897d63d368cfface0f5fc7dece4b8480cd
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742537"
---
# <a name="vnet-peering-in-azure-stack-hub-with-vms"></a>VM 所在的 Azure Stack Hub 中的 VNET 对等互连

可以在同一 Azure Stack Hub 环境中将两个 Azure Stack Hub VNet 互相连接。 目前无法使用内置[虚拟网关](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences)连接 Azure Stack Hub VNet。 必须使用 NVA 设备在两个 Azure Stack Hub VNet 之间创建 VPN 隧道。 在本文的模板引用中，部署了两个安装了 RRAS 的 Windows Server 2016 VM。 这两个 RRAS 服务器经过配置，可以在两个 VNET 之间实现 S2SVPN IKEv2 隧道。 创建适当的 NSG 和 UDR 规则以允许在指定为“内部”  的每个 VNET 上的子网之间进行路由。 

此部署模式是一个基础，它使得 VPN 隧道不仅可以在 Azure Stack Hub 实例内部创建，而且还可以在 Azure Stack Hub 实例之间创建，以及创建到本地网络等其他资源（通过使用 Windows RRAS S2S VPN 隧道）。 

可以在 [Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
) GitHub 存储库中找到这些模板。 该模板位于 **S2SVPNTunnel** 文件夹中。

![替换文字](./media/azure-stack-network-howto-vnet-peering/overview.svg)

## <a name="requirements"></a>要求

- 应用了最新更新的部署。 
- 所需的 Azure Stack Hub 市场项：
    -  Windows Server 2016 Datacenter（建议使用最新内部版本）
    -  自定义脚本扩展

## <a name="things-to-consider"></a>注意事项

- 某个网络安全组将应用到模板隧道子网。 建议使用其他 NSG 保护每个 VNet 中的内部子网。
- RDP“拒绝”规则将应用到隧道 NSG，如果你想要通过公共 IP 地址访问 VM，则需要将此规则设置为“允许”
- 此解决方案不考虑 DNS 解析
- VNet 名称和 vmName 的组合必须少于 15 个字符
- 此模板设计用于为 VNet1 和 VNet2 自定义 VNet 名称
- 此模板使用的是 BYOL windows
- 删除资源组时，目前在 (1907) 上必须手动将 NSG 与隧道子网分离，以确保删除资源组完成
- 此模板使用的是 DS3v2 VM。 RRAS 服务将安装并运行 Windows 内部 SQL Server。 如果 VM 太小，则可能会导致内存问题。 在减小 VM 大小之前，请验证性能。
- 这不是一个高度可用的解决方案。 如果需要更高可用性样式的解决方案，可以添加第二个 VM，必须手动将路由表中的路由更改为辅助接口的内部 IP。 还需要配置多个隧道以建立交叉连接。

## <a name="options"></a>选项

- 可以通过 _artifactsLocation 和 _artifactsLocationSasToken 参数使用自己的 Blob 存储帐户和 SAS 令牌。
- 此模板上有两个输出 INTERNALSUBNETREFVNET1 和 INTERNALSUBNETREFVNET2，这是内部子网的资源 ID（如果要在管道样式部署模式中使用）。

此模板为 VNet 命名和 IP 寻址提供默认值。 它需要管理员 (rrasadmin) 的密码，还提供了将自己的存储 blob 与 SAS 令牌配合使用的功能。 请谨慎地使这些值保持在合法的范围内，否则部署可能失败。 PowerShell DSC 包将在每个 RRAS VM 上执行，并安装路由和所有必需的依赖服务和功能。 如果需要，可以进一步自定义此 DSC。 自定义脚本扩展运行以下脚本，`Add-Site2Site.ps1` 使用共享密钥在两个 RRAS 服务器之间配置 VPNS2S 隧道。 可以查看自定义脚本扩展的详细输出，以查看 VPN 隧道配置的结果

![替换文字](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.svg)

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  