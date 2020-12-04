---
title: 对 AKS 进行故障排除
description: 本文提供有关 Azure Stack HCI 上的 Azure Kubernetes 服务 (AKS) 故障排除的信息。
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 26edd1f52b5a3d695fa70493606c1e2438feda78
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557083"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上的 Azure Kubernetes 服务故障排除

使用 Azure Stack HCI 上的 Azure Kubernetes 服务创建或管理 Kubernetes 群集时，可能偶尔会遇到问题。 本文提供解决这些问题的故障排除指南。

## <a name="troubleshooting-azure-stack-hci"></a>Azure Stack HCI 故障排除
若要对 Azure Stack HCI 群集中所有服务器上的网络和存储 QoS（服务质量）设置的群集验证报表进行故障排除，并验证是否已定义重要规则，请参阅[对群集验证报表进行故障排除](../hci/manage/validate-qos.md)。

若要了解如何排查 CredSSP 的问题，请参阅[对 CredSSP 进行故障排除](../hci/manage/troubleshoot-credssp.md)。

## <a name="troubleshooting-windows-admin-center"></a>Windows Admin Center 疑难解答
此产品处于公共预览状态，这意味着它仍处于开发阶段。 目前，Windows Admin Center Azure Kubernetes 服务扩展存在一些问题： 
* 目前，用来设置 Azure Stack HCI 上的 Azure Kubernetes 服务的系统群集中的每个服务器都必须是受信任的服务器。 因此 Windows Admin Center 必须能够在群集中的每个（而不只是一个或数个）服务器上运行 CredSSP 操作。 
* 如果遇到“`msft.sme.aks couldn't load`”错误，并且该错误指出加载区块失败，请使用最新版 Microsoft Edge 或 Google Chrome 并重试。
* 在启动用于设置 Azure Kubernetes 服务主机的向导或用于创建 Kubernetes 群集的向导之前，应通过 Windows Admin Center 登录到 Azure。 在工作流中，你可能需要重新登录。 如果在通过 Windows Admin Center 登录到 Azure 时遇到困难，请尝试从其他源（如 [Azure 门户](https://portal.azure.com/)）登录到 Azure 帐户。 如果仍然遇到问题，请在与支持部门联系之前，查看 [Windows Admin Center 已知问题](/windows-server/manage/windows-admin-center/support/known-issues)一文。
* 在通过 Windows Admin Center 进行的 Azure Stack HCI 上 Azure Kubernetes 服务部署的当前迭代中，只有设置 Azure Kubernetes 服务主机的用户才可以在系统上创建 Kubernetes 群集。 若要解决此问题，请将 .wssd 文件夹从设置 Azure Kubernetes 服务主机的用户配置文件复制到将要创建新 Kubernetes 群集的用户配置文件。
* 如果在任一向导中收到一个有关错误配置的错误，请执行群集清理操作。 这些操作可能包含删除 C:\Program Files\AksHci\mocctl.exe 文件。
* 若要使 CredSSP 在群集创建向导中成功地发挥作用，Windows Admin Center 必须由同一帐户安装和使用。 如果使用某个帐户安装 Windows Admin Center 之后，在使用它时使用的是其他帐户，会出现错误。
* 在群集部署过程中，helm.zip 文件传输可能会出现问题。 此问题通常会导致错误，该错误会指出 helm.zip 文件的路径不存在或无效。 若要解决此问题，请重试部署。
* 如果部署在很长时间内处于挂起状态，则表明可能存在 CredSSP 问题或连接性问题。 请尝试通过执行以下步骤来排查部署问题： 
    1.  在运行 Windows Admin Center 的计算机上的 PowerShell 窗口中运行以下命令： 
          ```PowerShell
          Enter-PSSession <servername>
          ```
    2.  如果此命令成功，则表明你可以连接到服务器，没有连接性问题。
    
    3.  如果存在 CredSSP 问题，请运行此命令来测试网关计算机与目标计算机之间的信任： 
          ```PowerShell
          Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
          ``` 
        还可以运行以下命令来测试在访问本地网关时的信任： 
          ```PowerShell
          Enter-PSSession -computer localhost -credential (Get-Credential)
          ``` 
* 如果使用的是 Azure Arc 且有多个租户 ID，请在部署之前运行以下命令指定所需租户。 否则，部署可能会失败。

   ```Azure CLI
   az login –tenant <tenant>
   ```
* 如果刚刚创建了新的 Azure 帐户，且尚未在网关计算机上登录到该帐户，则可能会在将 Windows Admin Center 网关注册到 Azure 时遇到问题。 若要缓解此问题，请在另一个浏览器标签页或窗口中登录到 Azure 帐户，然后将 Windows Admin Center 网关注册到 Azure。

### <a name="creating-windows-admin-center-logs"></a>创建 Windows Admin Center 日志
当你报告 Windows Admin Center 存在的问题时，最好附加日志以帮助开发团队诊断问题。 Windows Admin Center 中的错误通常以两种形式之一出现： 
- 运行 Windows Admin Center 的计算机上的事件查看器中显示的事件 
- 浏览器控制台中出现的 JavaScript 问题 

若要收集 Windows Admin Center 的日志，请使用公共预览版包中提供的 Get-SMEUILogs.ps1 脚本。 
 
若要使用该脚本，请在存储脚本的文件夹中运行此命令： 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
该命令有以下参数：
 
* `-ComputerNames`：要从中收集日志的计算机的列表。
* `-Destination`：要将日志聚合到的计算机。
* `-HoursAgo`：收集日志的开始时间，以运行脚本之前的小时数表示。
* `-NoCredentialPrompt`：一个开关，用于在当前环境中关闭凭据提示并使用默认凭据。
 
如果在运行此脚本时遇到困难，可以运行以下命令来查看帮助文本： 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Windows 工作器节点故障排除 
若要使用 SSH 登录 Windows 工作节点，请先通过运行获取节点的 IP 地址， `kubectl get` 并捕获 `EXTERNAL-IP` 值。

[!NOTE] 
必须将正确的位置传递到 SSH 私钥。 以下示例使用%systemdrive%\akshci \. ssh \ akshci_rsa 的默认位置，但是，如果通过指定的参数请求了其他路径，则可能需要更改此位置 `-sshPublicKey` `Set-AksHciConfig` 。

获取 Windows 辅助角色节点的 IP 地址：  

```
kubectl --kubeconfig=yourkubeconfig get nodes -o wide
```  

使用 `ssh Administrator@ip` SSH 连接到 Windows 节点：  

```
ssh -i $env:SYSTEMDRIVE\AksHci\.ssh\akshci_rsa administrator@<IP Address of the Node>
```
  
通过 SSH 连接到节点后，可以运行 `net user administrator *` 来更新管理员密码。 


## <a name="troubleshooting-linux-worker-nodes"></a>Linux 工作器节点故障排除 
若要使用 SSH 登录到 Linux 工作节点，请先通过运行获取节点的 IP 地址， `kubectl get` 并捕获 `EXTERNAL-IP` 值。

[!NOTE]
必须将正确的位置传递到 SSH 私钥。 以下示例使用%systemdrive%\akshci \. ssh \ akshci_rsa 的默认位置，但是，如果通过指定的参数请求了其他路径，则可能需要更改此位置 `-sshPublicKey` `Set-AksHciConfig` 。

获取 Linux 辅助角色节点的 IP 地址：  

```
kubectl --kubeconfig=yourkubeconfig get nodes -o wide
```  

使用 `ssh clouduser@ip` SSH 连接到 Linux 节点： 

```
ssh -i $env:SYSTEMDRIVE\AksHci\.ssh\akshci_rsa clouduser@<IP Address of the Node>
```  

通过 SSH 连接到节点后，可以运行 `net user administrator *` 来更新管理员密码。 

## <a name="troubleshooting-azure-arc-kubernetes"></a>Azure Arc Kubernetes 故障排除
若要了解有关与连接、权限和 Arc 代理相关的常见方案的疑难解答，请参阅 [启用 Azure Arc Kubernetes 故障排除](/azure/azure-arc/kubernetes/troubleshooting)。

## <a name="next-steps"></a>后续步骤
如果在使用 Azure Stack HCI 上的 Azure Kubernetes 服务时仍然遇到问题，可以通过 [GitHub](https://aka.ms/aks-hci-issues) 提交 bug。
