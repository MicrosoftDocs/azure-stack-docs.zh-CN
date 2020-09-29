---
title: 故障排除
description: Azure Stack HCI 上的 Azure Kubernetes 服务故障排除指南
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 312431b1ae36debc79b1a9bd3a874b648dfc9cd9
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948841"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上的 Azure Kubernetes 服务故障排除

使用 Azure Kubernetes 服务在 Azure Stack HCI 上创建或管理 Kubernetes 群集时，可能偶尔会遇到问题。 下面是帮助你解决这些问题的疑难解答指南。 

## <a name="troubleshooting-azure-stack-hci"></a>Azure Stack HCI 进行故障排除
若要在 Azure Stack HCI 群集中的服务器之间对网络和存储 QoS 的群集验证报告 (质量) 设置进行故障排除，并验证是否定义了重要规则，请访问 [排查群集验证报告问题](/azure-stack/hci/manage/validate-qos)。

若要解决 CredSSP 的问题，请访问 [对 Credssp 进行故障排除](/azure-stack/hci/manage/troubleshoot-credssp)。

## <a name="troubleshooting-windows-admin-center"></a>Windows Admin Center 疑难解答
此产品目前处于公共预览状态，这意味着它仍处于开发阶段。 目前，Windows 管理中心 Azure Kubernetes 服务扩展出现了几个问题： 
* 当前用于在 Azure Stack HCI 上设置 Azure Kubernetes 服务的系统群集中的每个服务器都必须是受信任的服务器。 这意味着，Windows 管理中心必须能够在群集中的每个服务器上执行 CredSSP 操作，而不只是其中的一个或多个服务器。 
* 如果遇到错误 `msft.sme.aks couldn't load` ，并且错误指出加载区块失败，请使用最新版本的 Edge 或 Google Chrome，然后重试。
* 在启动 Azure Kubernetes Service 主机设置向导或创建 Kubernetes 群集向导之前，应通过 Windows 管理中心登录到 Azure。 在工作流中可能需要重新签名。 如果在通过 Windows 管理中心登录到 Azure 时遇到困难，请尝试从其他源（如 [Azure 门户](https://portal.azure.com/)）登录到 azure 帐户。 如果继续遇到问题，请先查看 [Windows 管理中心的已知问题](/windows-server/manage/windows-admin-center/support/known-issues) 一文，然后再联系以获得支持。
* 在通过 Windows 管理中心 Azure Stack HCI 部署上的 Azure Kubernetes 服务的当前迭代中，仅设置 Azure Kubernetes 服务主机的用户可以在系统上创建 Kubernetes 群集。 若要解决此问题，请将 `.wssd` 设置 Azure Kubernetes Service 主机的用户配置文件中的文件夹复制到将启动新的 Kubernetes 群集的用户配置文件。
* 如果在向导中收到有关错误配置的错误，请执行群集清理操作。 这可能涉及到删除 `C:\Program Files\AksHci\mocctl.exe` 文件。
* 为了使 CredSSP 在群集创建向导中成功运行，必须使用同一帐户安装和使用 Windows 管理中心。 使用一个帐户进行安装，然后尝试将其与另一个帐户一起使用会导致错误。
* 在群集部署过程中，helm.zip 文件传输可能存在问题。 这通常会导致错误，指出 helm.zip 文件的路径不存在或无效。 若要解决此问题，请返回，然后重试部署。
* 如果你的部署在一段较长时间内挂起，你可能会遇到 CredSSP 或连接问题。 尝试执行以下步骤来排查部署问题： 
    1.  在运行 WAC 的计算机上，在 PowerShell 窗口中运行以下命令： 
    ```PowerShell
    Enter-PSSession <servername>
    ```
    2.  如果此命令成功，则可以连接到服务器，并且不会出现连接问题。
    
    3.  如果运行的是 CredSSP 问题，请运行以下命令来测试网关计算机与目标计算机之间的信任关系： 
    ```PowerShell
    Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
    ``` 
    你还可以运行以下命令来测试访问本地网关时的信任： 
    ```PowerShell
    Enter-PSSession -computer localhost -credential (Get-Credential)
    ``` 
* 如果你使用的是 Azure Arc 并且有多个租户 Id，请运行以下命令，在部署之前指定你所需的租户。 否则，可能会导致部署失败。

```Azure CLI
az login –tenant <tenant>
```
* 如果你刚创建了一个新的 Azure 帐户，并且尚未登录到你的网关计算机上的帐户，则在将 WAC 网关注册到 Azure 时可能会遇到问题。 若要缓解此问题，请在另一个浏览器选项卡或窗口中登录到 Azure 帐户，并将 WAC 网关注册到 Azure。

### <a name="creating-windows-admin-center-logs"></a>创建 Windows 管理中心日志
在 Windows 管理中心报告问题时，附加日志会很有帮助，因此开发团队可以诊断问题。 Windows 管理中心中的错误通常分为两种形式：在运行 Windows 管理中心的计算机上的事件查看器中显示的事件，或在浏览器控制台中显示的 JavaScript 问题。 若要收集 Windows 管理中心的日志，请使用 `Get-SMEUILogs.ps1` 公共预览版包中提供的脚本。 
 
若要使用该脚本，请在存储脚本的文件目录中运行以下命令： 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
该命令具有以下参数：
 
* **-ComputerNames**：要从中收集日志的计算机的列表。
* **-Destination**：要聚合日志的计算机。
* **-HoursAgo**：此参数定义要从中收集日志的脚本运行时前的小时数。
* **-NoCredentialPrompt**：这是一个开关，用于在当前环境中关闭凭据提示并使用默认凭据。
 
如果在运行此脚本时遇到困难，可以运行以下命令来查看帮助文本： 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Windows 工作节点疑难解答 
若要登录到 Windows 辅助角色节点，请先通过运行获取节点的 IP 地址， `kubectl get` 并记下 `EXTERNAL-IP` 值：

```PowerShell
kubectl get nodes -o wide
``` 
使用 SSH 连接到节点 `ssh Administrator@ip` 。 通过 ssh 连接到节点后，可以运行 `net user administrator *` 来更新管理员的密码。 

## <a name="troubleshooting-linux-worker-nodes"></a>Linux 工作节点故障排除 
若要登录到 Linux 辅助角色节点，请先通过运行获取节点的 IP 地址， `kubectl get` 并记下 `EXTERNAL-IP` 值：

```PowerShell
kubectl get nodes -o wide
``` 
使用 SSH 连接到节点 `ssh clouduser@ip` 。 

## <a name="troubleshooting-azure-arc-for-kubernetes"></a>排查 Kubernetes 的 Azure Arc 问题
若要解决与连接、权限和 Arc 代理相关的一些常见方案，请访问 [启用了 Azure Arc Kubernetes 的故障排除](/azure/azure-arc/kubernetes/troubleshooting)。

## <a name="next-steps"></a>后续步骤
如果在 Azure Stack HCI 上使用 Azure Kubernetes 服务时遇到问题，请通过 [GitHub](https://aka.ms/aks-hci-issues)进行文件错误。  
