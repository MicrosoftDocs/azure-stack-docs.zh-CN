---
title: 部署网络流量
titleSuffix: Azure Stack Hub
description: 了解在部署 Azure Stack Hub 期间的网络流量流。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: a663544b21fee671e57483e13030639c7b5edd3b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81243759"
---
# <a name="deployment-network-traffic"></a>部署网络流量

了解 Azure Stack Hub 部署期间的网络流量有助于使部署成功。 本文将引导你逐步了解部署过程中的网络流量，以便你知道应出现的结果。

下图描绘了部署过程中涉及的所有组件和连接：

![Azure Stack Hub 部署网络拓扑](media/deployment-networking/figure1.svg)

> [!NOTE]
> 本文介绍了联网部署的要求。 若要了解其他部署方法，请参阅 [Azure Stack Hub 部署连接模型](azure-stack-connection-models.md)。

## <a name="the-deployment-vm"></a>部署 VM

Azure Stack Hub 解决方案包括一组用于托管 Azure Stack Hub 组件的服务器，以及一个额外的称为“硬件生命周期主机 (HLH)”的服务器。 此服务器用于部署和管理解决方案的生命周期，并在部署过程中托管部署 VM (DVM)。

Azure Stack Hub 解决方案提供商可以预配其他管理 VM。 在解决方案提供商对管理 VM 进行任何更改之前，请与解决方案提供商确认。

## <a name="deployment-requirements"></a>部署要求

在部署开始之前，必须满足一些最低要求。这些要求可以由 OEM 进行验证，目的是确保部署成功完成：

- [证书](azure-stack-pki-certs.md)。
- [Azure 订阅](azure-stack-validate-registration.md)。 你可能需要检查订阅。
- Internet 访问。
- DNS。
- NTP。

> [!NOTE]
> 本文重点介绍后三个要求。 有关头两个要求的详细信息，请查看上面的链接。

## <a name="about-deployment-network-traffic"></a>关于部署网络流量

DVM 配置了一个来自 BMC 网络的 IP，要求对 Internet 进行网络访问。 虽然并不是所有 BMC 网络组件都要求对 Internet 进行外部路由或访问，但某些特定于 OEM 的组件会利用来自此网络的 IP，可能也需要它。

在部署期间，DVM 会使用订阅中的 Azure 帐户通过 Azure Active Directory (Azure AD) 进行身份验证。 为此，DVM 需要对[特定端口和 URL](azure-stack-integrate-endpoints.md) 的列表进行 Internet 访问。 DVM 会利用 DNS 服务器将内部组件发出的 DNS 请求转发到外部 URL。 内部 DNS 将这些请求转发到你在部署之前提供给 OEM 的 DNS 转发器地址。 这同样适用于 NTP 服务器，需要一个可靠的时间服务器来维护所有 Azure Stack Hub 组件的一致性和时间同步。

部署期间 DVM 所需的 Internet 访问仅限出站访问，不得在部署期间进行入站调用。 请注意，它使用 IP 作为源，且 Azure Stack Hub 不支持代理配置。 因此，必要时需提供透明代理或 NAT 来访问 Internet。 在部署期间，一些内部组件将开始使用公共 VIP 通过外部网络访问 Internet。 部署完以后，Azure 和 Azure Stack Hub 之间的所有通信都使用公共 VIP 通过外部网络来完成。

Azure Stack Hub 交换机上的网络配置包含访问控制列表 (ACL)，用于限制特定网络源和目标之间的流量。 DVM 是访问不受限的唯一组件；即使 HLH 也受到限制。 可以向 OEM 询问自定义选项，以便更容易地从网络进行管理和访问。 由于存在这些 ACL，因此必须确保在部署时不更改 DNS 和 NTP 服务器地址， 否则需重新配置解决方案的所有交换机。

部署完成后，系统的组件将继续使用外部网络通过 SDN 使用提供的 DNS 和 NTP 服务器地址。 例如，如果在部署完成以后查看 DNS 请求，会发现源从 DVM IP 更改为公共 VIP。

## <a name="next-steps"></a>后续步骤

[验证 Azure 注册](azure-stack-validate-registration.md)
