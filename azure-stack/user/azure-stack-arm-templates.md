---
title: 在 Azure Stack 中使用 Azure 资源管理器模板 | Microsoft Docs
description: 了解如何在 Azure Stack 中使用 Azure 资源管理器模板来部署资源。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: 0b9aedb4a6b046755192b84e18e8c75a8d015c8f
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974075"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>在 Azure Stack 中使用 Azure 资源管理器模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可使用 Azure 资源管理器 模板通过单个协调操作部署和预配应用程序的所有资源。 还可以重新部署模板，对资源组中的资源进行更改。

可以通过 Microsoft Azure Stack 门户、PowerShell、命令行和 Visual Studio 部署这些模板。

[GitHub 中提供了](https://aka.ms/azurestackgithub)以下快速入门模板：

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>部署 SharePoint Server（非高可用性部署）

使用 PowerShell [Desired State Configuration](/powershell/dsc/overview/overview) (DSC) 扩展[创建包含以下资源的 SharePoint Server 2013 场](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha)：

* 虚拟网络
* 三个存储帐户
* 两个外部负载均衡器
* 一个虚拟机 (VM)，配置为包含单个域的新林的域控制器
* 一个 VM，配置为 SQL Server 2014 独立服务器
* 一个 VM，配置为单机 SharePoint Server 2013 场

## <a name="deploy-ad-non-high-availability-deployment"></a>部署 AD（非高可用性部署）

使用 PowerShell DSC 扩展[创建包含以下资源的 AD 域控制器服务器](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha)：

* 虚拟网络
* 一个存储帐户
* 一个外部负载均衡器
* 一个虚拟机 (VM)，配置为包含单个域的新林的域控制器

## <a name="deploy-adsql-non-high-availability-deployment"></a>部署 AD/SQL（非高可用性部署）

使用 PowerShell DSC 扩展[创建包含以下资源的 SQL Server 2014 独立服务器](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha)：

* 虚拟网络
* 两个存储帐户
* 一个外部负载均衡器
* 一个虚拟机 (VM)，配置为包含单个域的新林的域控制器
* 一个 VM，配置为 SQL Server 2014 独立服务器

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

使用 PowerShell DSC 扩展配置现有的虚拟机本地配置管理器 (LCM)，将其注册到 Azure 自动化帐户 DSC 提取服务器。

## <a name="create-a-virtual-machine-from-a-user-image"></a>从用户映像创建虚拟机

[从自定义用户映像创建虚拟机](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage)。 此模板还会部署虚拟网络（使用 DNS）、公共 IP 地址和网络接口。

## <a name="basic-virtual-machine"></a>基本虚拟机

[部署一个包含虚拟网络（使用 DNS）、公共 IP 地址和网络接口的 Windows VM](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm)。

## <a name="cancel-a-running-template-deployment"></a>取消正在运行的模板部署

若要取消正在运行的模板部署，请使用 [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) PowerShell [cmdlet](/powershell/developer/cmdlet/cmdlet-overview)。

## <a name="next-steps"></a>后续步骤

* [通过门户部署模板](azure-stack-deploy-template-portal.md)
* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
* [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)
