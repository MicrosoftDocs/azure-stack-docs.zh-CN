---
title: 部署网络流量
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 中心部署期间的网络流量。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: b2acff92365cfd9904a033c381bf85ea843d6b30
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366392"
---
# <a name="deployment-network-traffic"></a>部署网络流量

了解 Azure Stack 中心部署期间的网络流量有助于使部署成功。 本文将指导你完成部署过程中的网络流量，让你了解所需内容。

此图显示了部署过程中涉及的所有组件和连接：

![Azure Stack 中心部署网络拓扑](media/deployment-networking/figure1.png)

> [!NOTE]
> 本文介绍已连接部署的要求。 若要了解有关其他部署方法的信息，请参阅[Azure Stack 中心部署连接模型](azure-stack-connection-models.md)。

## <a name="the-deployment-vm"></a>部署 VM

Azure Stack 集线器解决方案包含一组服务器，这些服务器用于承载 Azure Stack 集线器组件和额外的服务器（称为硬件生命周期主机（HLH））。 此服务器用于部署和管理解决方案的生命周期，并托管部署 VM （DVM）。

Azure Stack 集线器解决方案提供商可以预配更多的管理 Vm。 在从解决方案提供商处对管理 Vm 进行任何更改之前，请与解决方案提供商确认。

## <a name="deployment-requirements"></a>部署要求

部署开始之前，OEM 可以验证一些最低要求，以确保部署成功完成：

- [证书](azure-stack-pki-certs.md)。
- [Azure 订阅](azure-stack-validate-registration.md)。 你可能需要检查你的订阅。
- Internet 访问。
- DNS。
- NTP.

> [!NOTE]
> 本文重点介绍最后三个要求。 有关前两个版本的详细信息，请参阅上面的链接。

## <a name="about-deployment-network-traffic"></a>关于部署网络流量

使用来自 BMC 网络的 IP 配置 DVM，并要求对 internet 进行网络访问。 尽管并非所有 BMC 网络组件都需要外部路由或对 internet 的访问，但使用此网络中的 Ip 的某些特定 OEM 组件也可能需要使用它。

在部署过程中，DVM 将使用订阅中的 Azure 帐户对 Azure Active Directory （Azure AD）进行身份验证。 为此，DVM 要求通过 internet 访问[特定端口和 url](azure-stack-integrate-endpoints.md)的列表。 DVM 将利用 DNS 服务器将内部组件发出的 DNS 请求转发到外部 Url。 内部 DNS 将这些请求转发到部署之前提供给 OEM 的 DNS 转发器地址。 NTP 服务器同样适用：需要可靠的时间服务器来维护所有 Azure Stack 中心组件的一致性和时间同步。

在部署过程中 DVM 所需的 internet 访问仅限出站，部署过程中不会发出任何入站调用。 请记住，它使用其 IP 作为源，并且 Azure Stack 中心不支持代理配置。 因此，如有必要，需要提供透明代理或 NAT 来访问 internet。 在部署期间，某些内部组件将使用公共 Vip 通过外部网络开始访问 internet。 部署完成后，Azure 与 Azure Stack 中心之间的所有通信都将使用公共 Vip 通过外部网络进行。

Azure Stack 集线器交换机上的网络配置包含限制特定网络源与目标之间的流量的访问控制列表（Acl）。 DVM 是唯一具有不受限制的访问的组件;甚至限制了 HLH。 可以向 OEM 询问自定义选项，以简化网络的管理和访问。 由于这些 Acl，应避免在部署时更改 DNS 和 NTP 服务器地址，这一点很重要。 如果这样做，则需要重新配置解决方案的所有开关。

部署完成后，将继续使用外部网络通过 SDN 使用提供的 DNS 和 NTP 服务器地址。 例如，如果在部署完成后检查 DNS 请求，则源将从 DVM IP 更改为公共 VIP。

## <a name="next-steps"></a>后续步骤

[验证 Azure 注册](azure-stack-validate-registration.md)
