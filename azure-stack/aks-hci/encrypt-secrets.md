---
title: 加密 Azure Stack HCI 上的 AKS 上的 etcd 机密
description: 了解如何加密 Azure Stack HCI 上的 AKS 上的 etcd 机密
author: aabhathipsay
ms.topic: how-to
ms.date: 02/02/2021
ms.author: aabha
ms.reviewer: ''
ms.openlocfilehash: 82d40c2f97171196b90171e3dda850990bf2fbab
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116710"
---
# <a name="encrypt-etcd-secrets-on-aks-on-azure-stack-hci-clusters"></a>加密 Azure Stack HCI 群集上的 AKS 上的 etcd 机密

Kubernetes 中的机密是包含少量敏感数据的对象，例如密码和 SSH 密钥。 在 Kubernetes API 服务器中，机密存储在 _etcd_ 中，后者是用于所有群集数据的 Kubernetes 后备存储的高度可用密钥值存储。 启用 etcd 机密加密时，可以 [加密静态机密数据](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) ，还可以自动执行加密密钥的管理和轮换。 

> [!NOTE]
> 在此预览版本中，etcd 密码的加密仅适用于新的工作负荷群集。 

## <a name="enable-encryption-of-etcd-secrets"></a>启用 etcd 机密加密

使用 `-enableSecretsEncryption` [AksHciCluster](./new-akshcicluster.md) 命令的参数启用 etcd 密钥加密，并自动执行加密密钥轮替，如下所示： 

```powershell
New-AksHciCluster -name mynewcluster -enableSecretsEncryption
```

使用参数部署新群集后 `-enableSecretsEncryption` ，无法禁用此功能。

## <a name="monitor-and-troubleshoot"></a>监视和故障排除

若要在 Kubernetes 群集上简化应用程序部署，请查看可用的 [文档和脚本](https://github.com/microsoft/AKS-HCI-Apps) 。

- 若要使用 Elasticsearch、"熟知位" 和 "Kibana" 设置日志记录，请按照步骤 [安装工具并设置日志记录](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging)
- 若要使用监视工具 Prometheus，请遵循在[Kubernetes 群集中安装 Prometheus](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#certs-and-keys-monitoring)的步骤

> [!NOTE]
> 你可以在下的 "控制平面" 节点上找到日志 `/var/log/pods` 。

## <a name="additional-resources"></a>其他资源

- [加密静态机密数据](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data)
- [使用 KMS 提供程序进行数据加密](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)

## <a name="next-steps"></a>后续步骤

本操作方法指南介绍了如何对新的工作负荷群集启用 etcd 密码加密。 接下来可以：
- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows Server 应用程序](./deploy-windows-application.md)。
