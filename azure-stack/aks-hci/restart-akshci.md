---
title: Restart-AksHci
author: jessicaguan
description: Restart-AksHci PowerShell 命令在 Azure Stack HCI 上重新启动 AKS，并删除所有部署的 Kubernetes 群集。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 358ca36b085b21c6ecdbea7a024d9e72e0ca0597
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873621"
---
# <a name="restart-akshci"></a>Restart-AksHci

## <a name="synopsis"></a>摘要
在 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，并删除所有已部署的 Kubernetes 群集。

## <a name="syntax"></a>语法

### <a name="restart-aks-hci"></a>重新启动 AKS
```powershell
Restart-AksHci
```

## <a name="description"></a>说明
如果在 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，则将删除所有 Kubernetes 群集（如果有）以及 Azure Kubernetes 服务主机。 它还会从节点中卸载 Azure Stack HCI 代理和服务上的 Azure Kubernetes 服务。 然后，它会重复原始安装过程步骤，直到重新创建主机。 通过 Set-AksHciConfig 配置的 Azure Stack HCI 配置上的 Azure Kubernetes 服务会保留下载的 VHDX 映像。

## <a name="examples"></a>示例

### <a name="example"></a>示例
```powershell
PS C:\> Restart-AksHci
```
