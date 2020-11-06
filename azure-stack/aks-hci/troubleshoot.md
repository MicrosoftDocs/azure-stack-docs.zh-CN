---
title: AKS 故障排除
description: 本文提供了有关在 Azure Stack HCI 上对 Azure Kubernetes Service (AKS) 进行故障排除的信息。
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 4f13aff85c1444197fce5a01c62319026f844fe6
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415039"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上的 Azure Kubernetes 服务故障排除

在 Azure Stack HCI 上使用 Azure Kubernetes 服务创建或管理 Kubernetes 群集时，有时可能会遇到问题。 本文提供了疑难解答指南来帮助你解决这些问题。

## <a name="troubleshooting-azure-stack-hci"></a>Azure Stack HCI 故障排除
若要在 Azure Stack HCI 群集中的服务器之间对网络和存储 QoS 的群集验证报告 (质量) 设置进行故障排除，并验证是否定义了重要规则，请参阅 [群集验证报告故障排除](../hci/manage/validate-qos.md)。

若要了解有关 CredSSP 的疑难解答问题，请参阅 [Credssp 故障排除](../hci/manage/troubleshoot-credssp.md)。

## <a name="troubleshooting-windows-admin-center"></a>Windows Admin Center 疑难解答
此产品是公共预览版，这意味着它仍处于开发阶段。 Windows 管理中心 Azure Kubernetes 服务扩展目前有一些问题： 
* 目前，用于在 Azure Stack HCI 上设置 Azure Kubernetes 服务的系统群集中的每个服务器都必须是可信服务器。 因此，Windows 管理中心必须能够在群集中的每个服务器上运行 CredSSP 操作，而不只是在其中一个服务器上运行。 
* 如果收到错误消息 `msft.sme.aks couldn't load` ，并且错误指出加载区块失败，请使用最新版本的 Microsoft Edge 或 Google Chrome，然后重试。
* 在开始安装 Azure Kubernetes 服务主机或创建 Kubernetes 群集的向导之前，应通过 Windows 管理中心登录到 Azure。 在工作流过程中，你可能需要重新登录。 如果在通过 Windows 管理中心登录到 Azure 时遇到困难，请尝试从其他源（如 [Azure 门户](https://portal.azure.com/)）登录到 azure 帐户。 如果继续遇到问题，请在联系支持人员之前查看 [Windows 管理中心的已知问题](/windows-server/manage/windows-admin-center/support/known-issues) 一文。
* 在通过 Windows Admin Center 进行的 Azure Stack HCI 上 Azure Kubernetes 服务部署的当前迭代中，只有设置 Azure Kubernetes 服务主机的用户才可以在系统上创建 Kubernetes 群集。 若要解决此问题，请将 wssd 文件夹从设置 Azure Kubernetes Service 主机的用户的配置文件复制到将创建新 Kubernetes 群集的用户的配置文件。
* 如果在任一向导中收到一个有关错误配置的错误，请执行群集清理操作。 这些操作可能涉及到删除 C:\Program Files\AksHci\mocctl.exe 文件。
* 要使 CredSSP 在群集创建向导中成功运行，必须由同一帐户安装并使用 Windows 管理中心。 如果使用一个帐户来安装 Windows 管理中心，并尝试将其与另一个帐户一起使用，则会出现错误。
* 在群集部署过程中，可能会遇到 helm.zip 文件传输问题。 此问题通常会导致错误，指出 helm.zip 文件的路径不存在或无效。 若要解决此问题，请重试部署。
* 如果你的部署在一段较长时间内挂起，你可能会遇到 CredSSP 或连接问题。 请尝试以下步骤来排查部署问题： 
    1.  在运行 Windows 管理中心的计算机上，在 PowerShell 窗口中运行以下命令： 
          ```PowerShell
          Enter-PSSession <servername>
          ```
    2.  如果此命令成功，你可以连接到服务器，并且不会出现连接问题。
    
    3.  如果遇到 CredSSP 问题，请运行以下命令来测试网关计算机与目标计算机之间的信任关系： 
          ```PowerShell
          Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
          ``` 
        还可以运行以下命令来测试在访问本地网关时的信任： 
          ```PowerShell
          Enter-PSSession -computer localhost -credential (Get-Credential)
          ``` 
* 如果你使用的是 Azure Arc 并且有多个租户 Id，请运行以下命令，在部署之前指定你所需的租户。 否则，部署可能会失败。

   ```Azure CLI
   az login –tenant <tenant>
   ```
* 如果你刚创建了一个新的 Azure 帐户，但尚未登录到你的网关计算机上的帐户，则在使用 Azure 注册 Windows 管理中心网关时可能会遇到问题。 若要缓解此问题，请在另一个浏览器选项卡或窗口中登录到你的 Azure 帐户，然后将 Windows 管理中心网关注册到 Azure。

### <a name="creating-windows-admin-center-logs"></a>创建 Windows Admin Center 日志
当你报告 Windows 管理中心的问题时，最好附加日志以帮助开发团队诊断你的问题。 Windows 管理中心中的错误通常采用以下两种形式之一： 
- 在运行 Windows 管理中心的计算机上的事件查看器中显示的事件 
- Browser 控制台中显示的 JavaScript 问题 

若要收集 Windows 管理中心的日志，请使用公共预览版包中提供的 Get-SMEUILogs.ps1 脚本。 
 
若要使用该脚本，请在存储脚本的文件夹中运行以下命令： 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
该命令有以下参数：
 
* `-ComputerNames`：你要从中收集日志的计算机的列表。
* `-Destination`：要聚合日志的计算机。
* `-HoursAgo`：收集日志的开始时间，用小时数表示运行脚本之前的时间。
* `-NoCredentialPrompt`：用于在当前环境中关闭凭据提示并使用默认凭据的开关。
 
如果在运行此脚本时遇到困难，可以运行以下命令来查看帮助文本： 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Windows 工作器节点故障排除 
若要登录到 Windows 辅助角色节点，请先通过运行获取节点的 IP 地址 `kubectl get` 。 请记下 `EXTERNAL-IP` 值。

```PowerShell
kubectl get nodes -o wide
``` 
使用 SSH 连接到节点 `ssh Administrator@ip` 。 通过 SSH 连接到节点后，可以运行 `net user administrator *` 来更新管理员密码。 

## <a name="troubleshooting-linux-worker-nodes"></a>Linux 工作器节点故障排除 
若要登录到 Linux 辅助角色节点，请先通过运行获取节点的 IP 地址 `kubectl get` 。 请记下 `EXTERNAL-IP` 值。

```PowerShell
kubectl get nodes -o wide
``` 
使用 SSH 连接到节点 `ssh clouduser@ip` 。 

## <a name="troubleshooting-azure-arc-kubernetes"></a>Azure Arc Kubernetes 故障排除
若要了解有关与连接、权限和 Arc 代理相关的常见方案的疑难解答，请参阅 [启用 Azure Arc Kubernetes 故障排除](/azure/azure-arc/kubernetes/troubleshooting)。

## <a name="next-steps"></a>后续步骤
如果在 Azure Stack HCI 上使用 Azure Kubernetes 服务时遇到问题，则可以通过 [GitHub](https://aka.ms/aks-hci-issues)对 bug 进行归档。