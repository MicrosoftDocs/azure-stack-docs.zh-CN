---
title: AksHci PowerShell 模块
description: 了解如何使用 AksHci 模块命令在 Azure Stack HCI 上管理 AKS
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 511b3ad0dcf791550f40136a73f47114e9784bbe
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116744"
---
# <a name="akshci"></a>AksHci 

用于与 Azure Kubernetes 服务交互 Azure Stack HCI 的命令。

## <a name="akshci-cmdlets"></a>AksHci cmdlet

|         |            |
| ------- | ---------- |
| [akshcicluster](get-akshcicluster.md) | 列出包含 Azure Kubernetes 服务主机的已部署群集。 |
| [akshciclusterupgrades](get-akshciclusterupgrades.md) | 获取 Azure Kubernetes 服务群集的可用升级。 |
| [akshciconfig](get-akshciconfig.md) | 列出 Azure Kubernetes 服务主机的当前配置设置。 |
| [akshcicredential](get-akshcicredential.md) | 使用 kubectl 访问群集。 |
| [akshcieventlog](get-akshcieventlog.md) | 从 AKS HCI PowerShell 模块获取所有事件日志。 |
| [akshcikubernetesversion](get-akshcikubernetesversion.md) | 列出用于创建托管 Kubernetes 群集的可用版本。 |
| [akshcilogs](get-akshcilogs.md) | 使用所有 pod 中的日志创建一个压缩文件夹。 |
| [akshciupdates](get-akshciupdates.md) | 列出 Azure Stack HCI 上的 Azure Kubernetes 服务的可用更新。 |
| [akshciversion](get-akshciversion.md) | 获取 Azure Stack HCI 上的 Azure Kubernetes 服务的当前版本。 |
| [akshcivmsize](get-akshcivmsize.md) | 列出受支持的 VM 大小。 |
| [akshcinode](initialize-akshcinode.md) | 在每个物理节点上运行检查，查看是否满足所有要求，以便在 Azure Stack HCI 上安装 Azure Kubernetes 服务。 |
| [安装-akshci](install-akshci.md) | Azure Stack HCI 代理/服务和主机上安装 Azure Kubernetes 服务。 |
| [安装-akshciadauth](install-akshciadauth.md) | 安装 Active Directory 身份验证。 |
| [安装-akshciarconboarding](install-akshciarconboarding.md) | 下载并安装 kubectl 和 Kubernetes 命令行工具。 |
| [新-akshcicluster](new-akshcicluster.md) | 创建新的托管 Kubernetes 群集。 |
| [新-akshcinetworksetting](new-akshcinetworksetting.md) | 为新的虚拟网络创建对象。 |
| [akshcicluster](remove-akshcicluster.md) | 删除托管的 Kubernetes 群集。 |
| [重新启动-akshci](restart-akshci.md) | 在 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，并删除所有已部署的 Kubernetes 群集。 |
| [akshciclusternodecount](set-akshciclusternodecount.md) | 缩放群集中控制平面节点或辅助节点的数目。 |
| [akshciconfig](set-akshciconfig.md) | 设置或更新 Azure Kubernetes 服务主机的配置设置。 |
| [卸载-akshci](uninstall-akshci.md) | Azure Stack HCI 上删除 Azure Kubernetes 服务。 |
| [卸载-akshciadauth](uninstall-akshciadauth.md) | 删除 Active Directory 身份验证。 |
| [更新-akshci](update-akshci.md) | 将 Azure Kubernetes 服务主机更新到最新的 Kubernetes 版本。 |
| [更新-akshcicluster](update-akshcicluster.md) | 将托管的 Kubernetes 群集更新为较新的 Kubernetes 或 OS 版本。 |

