---
title: 使用 Windows PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机的快速入门
description: 了解如何使用 Windows PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 4211ec50ef0ea24ffb55f14791101c5d266ede2e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612550"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>快速入门：使用 PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机

> 适用于： Azure Stack HCI、Windows Server 2019 Datacenter

在本快速入门中，你将了解如何使用 PowerShell 设置 Azure Kubernetes 服务主机。 若要改为使用 Windows Admin Center，请参阅[使用 Windows Admin Center 进行设置](setup.md)。

## <a name="before-you-begin"></a>准备阶段

请确保具有以下各项之一：
 - 2-4 节点 Azure Stack HCI 群集
 - Windows Server 2019 Datacenter 故障转移群集
 - 单节点 Windows Server 2019 Datacenter 
 
在开始之前，请确保已满足[系统要求](.\system-requirements.md)页上的所有先决条件。 
建议使用 2-4 节点的 Azure Stack HCI 群集。 如果没有上述任何一种，请按照 [AZURE STACK HCI 注册页](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)上的说明进行操作。    

   > [!IMPORTANT]
   > 删除 Azure Stack HCI 上的 Azure Kubernetes 服务时，请参阅 [在 AZURE STACK hci 上删除 Azure Kubernetes 服务](#remove-azure-kubernetes-service-on-azure-stack-hci) ，并仔细按照说明进行操作。 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>步骤 1：下载并安装 AksHci PowerShell 模块

请从 [Azure Stack HCI 上的 Azure Kubernetes 服务注册页](https://aka.ms/AKS-HCI-Evaluate)下载 `AKS-HCI-Public-Preview-Dec-2020`。 zip 文件 `AksHci.Powershell.zip` 包含 PowerShell 模块。

如果以前使用 PowerShell 或 Windows 管理中心 Azure Stack HCI 上安装了 Azure Kubernetes 服务，则新的 PowerShell 模块有两个安装流程：
 - 执行 PowerShell 模块的干净安装，以便从干净系统开始，并删除以前部署的工作负荷。 若要执行全新安装，请执行步骤1.1。
 - 如果要使系统和工作负荷保持正确，请升级 PowerShell 模块。 若要升级 PowerShell 模块，请跳到步骤1.2。

### <a name="step-11-clean-install-of-the-akshci-powershell-module"></a>步骤1.1：清理安装 AksHci PowerShell 模块

在继续操作之前，请运行以下命令。
   ```powershell
   Uninstall-AksHci
   ```

关闭 PowerShell 窗口。 删除位于路径中的 AksHci、AksHci、MOC 和 MSK8sDownloadAgent 的任何现有目录 `%systemdrive%\program files\windowspowershell\modules` 。 删除现有目录后，可以提取新的 zip 文件的内容。 请确保在正确的位置 (`%systemdrive%\program files\windowspowershell\modules`) 提取 zip 文件。 然后，运行以下命令。

   ```powershell
   Import-Module AksHci
   ```

再次关闭所有 PowerShell 窗口，然后重新打开管理会话，并继续执行步骤 1.3-验证升级的 PowerShell 模块。

### <a name="step-12-upgrade-the-akshci-powershell-module"></a>步骤1.2：升级 AksHci PowerShell 模块

关闭 PowerShell 窗口。 删除位于路径中的 AksHci、AksHci、MOC 和 MSK8sDownloadAgent 的任何现有目录 `%systemdrive%\program files\windowspowershell\modules` 。 删除这些目录后，可以提取新 zip 文件的内容。 请确保在正确的位置 (`%systemdrive%\program files\windowspowershell\modules`) 提取 zip 文件。 然后，运行以下命令。

   ```powershell
   Import-Module AksHci
   ```
  
运行上述命令后，请关闭所有 PowerShell 窗口，然后重新打开管理会话以验证 PowerShell 模块升级，如下所述，然后运行该 `Update-AksHci` 命令，如本文档后面所述。

## <a name="step-13-validate-upgraded-powershell-module"></a>步骤1.3：验证升级的 PowerShell 模块

**关闭所有 powershell 窗口** ，然后重新打开新的管理会话，以检查是否安装了最新版本的 powershell 模块。  

   ```powershell
   Get-Command -Module AksHci
   ```
 
**输出：**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.12     AksHci
Function        Get-AksHciCluster                                  0.2.12     AksHci
Function        Get-AksHciConfig                                   0.2.12     AksHci
Function        Get-AksHciCredential                               0.2.12     AksHci
Function        Get-AksHciKubernetesVersion                        0.2.12     AksHci
Function        Get-AksHciLogs                                     0.2.12     AksHci
Function        Get-AksHciUpdates                                  0.2.12     AksHci
Function        Get-AksHciVersion                                  0.2.12     AksHci
Function        Get-AksHciVmSize                                   0.2.12     AksHci
Function        Install-AksHci                                     0.2.12     AksHci
Function        Install-AksHciAdAuth                               0.2.12     AksHci
Function        Install-AksHciArcOnboarding                        0.2.12     AksHci
Function        New-AksHciCluster                                  0.2.12     AksHci
Function        Remove-AksHciCluster                               0.2.12     AksHci
Function        Restart-AksHci                                     0.2.12     AksHci
Function        Set-AksHciClusterNodeCount                         0.2.12     AksHci
Function        Set-AksHciConfig                                   0.2.12     AksHci
Function        Uninstall-AksHci                                   0.2.12     AksHci
Function        Uninstall-AksHciAdAuth                             0.2.12     AksHci
Function        Uninstall-AksHciArcOnboarding                      0.2.12     AksHci
Function        Update-AksHci                                      0.2.12     AksHci
Function        Update-AksHciCluster                               0.2.12     AksHci
```

## <a name="step-2-prepare-your-machines-for-deployment"></a>步骤 2：准备要部署的计算机

在每个物理节点上运行检查，以查看所有要求是否都已得到满足，以便安装 Azure Stack HCI 上的 Azure Kubernetes 服务。

以管理员身份打开 PowerShell 并运行以下命令。

   ```powershell
   Initialize-AksHciNode
   ```

检查完以后，你会看到以绿色文本显示的“已完成”。

## <a name="step-3-configure-your-deployment"></a>步骤 3：配置部署

设置 Azure Kubernetes 服务主机的配置设置。 **如果要在2-4 节点 Azure Stack HCI 群集或 Windows Server 2019 Datacenter 故障转移群集，则必须指定 `imageDir` 和 `cloudConfigLocation` 参数。** 对于单节点 Windows Server 2019 Datacenter，所有参数都是可选的，并设置为其默认值。 但是，为了获得最佳性能，**建议使用 2-4 节点的 Azure Stack HCI 群集部署。**

使用以下命令配置部署。

   ```powershell
   Set-AksHciConfig [-imageDir <String>]
                    [-cloudConfigLocation <String>]
                    [-nodeConfigLocation <String>]
                    [-vnetName <String>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-sshPublicKey <String>]
                    [-vipPoolStartIp <String>]
                    [-vipPoolEndIp <String>]
                    [-macPoolStart <String>]
                    [-macPoolEnd <String>]
                    [-vlanID <int>]
                    [-kvaLoadBalancerType {unstacked_haproxy, stacked_kube_vip}]
                    [-kvaControlPlaneEndpoint <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerNoProxy <String>]
                    [-proxyServerCredential <PSCredential>]
                    [-cloudServiceCidr <String>]
                    [-workingDir <String>]
                    [-version <String>]
                    [-vnetType <String>]
                    [-nodeAgentPort <int>]
                    [-nodeAgentAuthorizerPort <int>]
                    [-clusterRoleName <String>]
                    [-cloudLocation <String>]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="example"></a>示例

若要在具有 DHCP 网络的2-4 节点群集上进行部署：

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
   ```

使用虚拟 IP 池进行部署：

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vipPoolStartIp 10.0.0.20 -vipPoolEndIp 10.0.0.80
   ```

若要部署 `stacked_kube_vip` 负载均衡器：

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -kvaLoadBalancerType stacked_kube_vip -kvaControlPlaneEndpoint 10.0.1.10
   ```

使用代理服务器进行部署：

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
   ```  

### <a name="optional-parameters"></a>可选参数

`-imageDir`

Azure Stack HCI 上的 Azure Kubernetes 服务将在其中存储 VHD 映像的目录的路径。 对于单节点部署，默认值为 `%systemdrive%\AksHciImageStore`。 对于多节点部署，必须指定此参数。 路径必须指向共享的存储路径（例如 `C:\ClusterStorage\Volume2\ImageStore` ）或 SMB 共享（例如 `\\FileShare\ImageStore`）。

`-cloudConfigLocation`

云代理将存储其配置的位置。 对于单节点部署，默认值为 `%systemdrive%\wssdcloudagent`。 此位置可以与上面的 `-imageDir` 路径相同。 对于多节点部署，必须指定此参数。 路径必须指向共享的存储路径（例如 `C:\ClusterStorage\Volume2\ImageStore` ）或 SMB 共享（例如 `\\FileShare\ImageStore`）。 此位置需要在高度可用的共享上，以便始终可以访问该存储。

`-nodeConfigLocation`

节点代理将存储其配置的位置。 每个节点都有一个节点代理，因此其配置在本地。 此位置必须是本地路径。 对于所有部署，默认值为 `%systemdrive%\programdata\wssdagent` 。

`-vnetName`

要将虚拟机与之连接的虚拟交换机的名称。 如果主机上已有外部交换机，应在此处传递交换机的名称。 如果该交换机不存在，则会创建它。默认为“外部”名称。  

`-controlPlaneVmSize`

要为控制平面创建的 VM 的大小。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-loadBalancerVmSize`

要为负载均衡器 VM 创建的 VM 的大小。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-sshPublicKey`

SSH 公钥文件的路径。 使用此公钥，你将能够登录到 Azure Stack HCI 部署上的 Azure Kubernetes 服务创建的任何 VM。 如果你有自己的 SSH 公钥，你将在此处传递其位置。 如果未提供任何密钥，我们将在下查找一个密钥 `%systemdrive%\akshci\.ssh\akshci_rsa.pub` 。 如果该文件不存在，将生成并使用上述位置的 SSH 密钥对。

`-vipPoolStartIp`

使用 VIP 池进行部署时，此参数将指定池的网络开始 IP。 应将 VIP 池用于长期部署，以保证 IP 地址池保持一致。 当你拥有始终需要访问的工作负荷时，这非常有用。 默认为无。

`-vipPoolEndIp`

使用 VIP 池进行部署时，此参数将指定池的网络结束 IP。 应将 VIP 池用于长期部署，以保证 IP 地址池保持一致。 当你拥有始终需要访问的工作负荷时，这非常有用。 默认为无。

`-macPoolStart` 

这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址开头。 MAC 地址的语法要求第一个字节的最小有效位应始终为0，第一个字节应始终为偶数 (，即00，02，04，06 ... ) 。典型的 MAC 地址如下所示：02：1E：2B：78：00：00。 将 MAC 池用于长期部署，以便分配的 MAC 地址保持一致。 如果要求 Vm 具有特定的 MAC 地址，这会很有用。 默认为无。

`-macPoolEnd`

这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址结尾。 MAC 地址的语法要求第一个字节的最小有效位应始终为0，第一个字节应始终为偶数 (，即00，02，04，06 ... ) 。作为传递的地址的第一个字节 `-macPoolEnd` 应与作为传递的地址的第一个字节相同 `-macPoolStart` 。 将 MAC 池用于长期部署，以便分配的 MAC 地址保持一致。 如果要求 Vm 具有特定的 MAC 地址，这会很有用。 默认为无。

`-vlandID`

这可以用来指定网络 VLAN ID。 Azure Kubernetes 服务主机和 Kubernetes 群集 VM 网络适配器将用提供的 VLAN ID 进行标记。 如果有需要标记为获得正确连接的特定 VLAN ID，则应使用此 ID。 默认为无。

`-kvaLoadBalancerType`

这将采用 `unstacked_haproxy` 或 `stacked_kube_vip` 。 `unstacked_haproxy` 默认情况下，将使用 HAProxy 作为 Azure Kubernetes 服务主机的 API 服务器终结点部署单独的负载平衡器 VM。 `stacked_kube_vip`是 Azure Kubernetes 服务主机的负载均衡器解决方案 [Kubevip](https://kube-vip.io/)。 它允许你将主机中的静态 IP 地址指定为跨控制平面节点的浮动 IP，以使 API 服务器通过 IP 高度可用。 如果选择此选项，则必须在参数中指定静态 IP 地址 `kvaControlPlaneEndpoint` ，并且不部署单独的负载均衡器 VM。

`stacked_kube_vip` 需要 IP 地址，并且通过节省内存、CPU 和部署时间来更好地了解资源。 如果没有 IP 地址使用作为浮动 IP，则应使用 `unstacked_haproxy` 。 后一种方法需要负载均衡器 VM。 

`-kvaControlPlaneEndpoint`

此参数设置为时，指定要用作 Azure Kubernetes Service 主机 API 服务器地址的静态 IP 地址 `kvaLoadBalancerType` `stacked_kube_vip` 。 如果 `stacked_kube_vip` 使用，则必须指定此参数。

`-proxyServerHTTP`

这会提供一个代理服务器 URI，该 URI 应由需要访问 HTTP 终结点的所有组件使用。 URI 格式包括 URI 架构、服务器地址和端口 (，即 https://server.com:8888) 。 默认为无。

`-proxyServerHTTPS`

这会提供一个代理服务器 URI，该 URI 应由需要访问 HTTPS 终结点的所有组件使用。 URI 格式包括 URI 架构、服务器地址和端口 (，即 https://server.com:8888) 。 默认为无。

`-proxyServerNoProxy`

这是将从代理中免除的地址的逗号分隔字符串。 默认值为 `localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16`。 这不包括来自代理服务器的本地主机流量 (localhost、127.0.0.1) 、内部 Kubernetes 服务流量 ( .svc) 、Kubernetes Service CIDR (10.96.0.0/12) 和 Kubernetes POD CIDR (10.244.0.0/16) 。 可以使用此参数添加更多子网范围或名称免除。 **此参数的设置非常重要，因为如果未正确配置，则可能会意外地将内部 Kubernetes 群集流量路由到代理。这可能导致网络通信失败。**


`-proxyServerCredential`

这会提供用户名和密码以向 HTTP/HTTPS 代理服务器进行身份验证。 您可以使用 `Get-Credential` 生成要 `PSCredential` 传递给此参数的对象。 默认为无。

`-cloudServiceCidr`

这可用于提供一个静态 IP/网络前缀来分配给 MOC CloudAgent 服务。 应使用 CIDR 格式提供此值。 （示例：192.168.1.2/16）。 你可能需要指定此项以确保始终可以访问网络上的任何重要内容，因为 IP 地址不会更改。 默认为无。

`-workingDir`

这是用于存储小文件的模块的工作目录。 默认为 `%PROGRAMFILES%\AksHci` ，大多数部署不应进行更改。建议不要更改默认值。 

`-version`

要部署的 Azure Stack HCI 上的 Azure Kubernetes 服务的版本。 默认值为最新版本。 建议不要更改默认值。

`-vnetType`

要连接或创建的虚拟交换机的类型。 此值默认为“外部”交换机类型。 建议不要更改默认值。

`-nodeAgentPort`

节点代理应侦听的 TCP/IP 端口号。 默认为 45000。建议不要更改默认值。 

`-nodeAgentAuthorizerPort`

节点代理为授权端口使用的 TCP/IP 端口号。 默认为 45001。 建议不要更改默认值。  

`-clusterRoleName`

此名称指定在将云代理创建为群集内的通用服务时要使用的名称。 此名称默认为具有 ca 前缀的唯一名称和 guid 后缀 (例如： "9e6eb299-bc0b-4f00-9fd7-942843820c26" ) 。 建议不要更改默认值。

`-cloudLocation` 

此参数提供自定义的 Microsoft 操作云位置名称。 默认名称为 "MocLocation"。 建议不要更改默认值。

`-skipHostLimitChecks`

在允许部署继续进行之前，请求脚本跳过它用于确认内存和磁盘空间是否可用的所有检查。 不建议使用此设置。

`-insecure`

在 Azure Stack HCI 组件（例如云代理和节点代理）上部署 Azure Kubernetes 服务，) 在不安全模式下 (s， (不会) TLS 安全连接。不建议在生产环境中使用不安全模式。

`-skipUpdates`

如果要跳过任何可用的更新，请使用此标志。 不建议使用此设置。

`-forceDnsReplication`

在某些系统上，DNS 复制最多可能需要一小时。 这会导致部署速度缓慢。 如果遇到此问题，你会看到 Install-AksHci 停滞在循环中。 若要解决此问题，请尝试使用此标志。 `-forceDnsReplication` 标志不是有保证的修补方法。 如果该标志背后的逻辑失败，则会隐藏该错误，并且命令将继续执行，如同未提供该标志一样。 

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>重置 Azure Stack HCI 上的 Azure Kubernetes 服务的配置

若要重置 Azure Stack HCI 配置上的 Azure Kubernetes 服务，请运行以下命令。 单独运行此命令会将配置重置为默认值。

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>步骤 4：启动新部署

配置你的部署后，你必须启动部署。 这会在 Azure Stack HCI 代理/服务和 Azure Kubernetes 服务主机上安装 Azure Kubernetes 服务。

若要开始部署，请运行以下命令。

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>验证已部署的 Azure Kubernetes 服务主机

若要确保 Azure Kubernetes 服务主机已部署，请运行以下命令。 部署 Kubernetes 群集后，还可以使用相同的命令获取这些群集。

```powershell
Get-AksHciCluster
```

**输出：**
```

Name            : clustergroup-management
Version         : v1.18.8
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>步骤 5：使用 kubectl 访问群集

若要使用 kubectl 访问 Azure Kubernetes 服务主机或 Kubernetes 群集，请运行以下命令。 这将使用指定群集的 kubeconfig 文件作为 kubectl 的默认 kubeconfig 文件。

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>示例

```powershell
Get-AksHciCredential -clusterName clustergroup-management
```

### <a name="required-parameters"></a>必需参数

`clusterName`

群集的名称。

### <a name="optional-parameters"></a>可选参数

`outputLocation`

要将 kubeconfig 下载到的位置。 默认值为 `%USERPROFILE%\.kube`。

## <a name="get-logs"></a>获取日志

若要从所有 Pod 获取日志，请运行以下命令。 此命令将在路径 `C:\wssd\akshcilogs` 中创建名为 `akshcilogs` 的输出压缩文件夹。

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>在 Azure Stack HCI 上更新到最新版本的 Azure Kubernetes 服务

若要在 Azure Stack HCI 上更新到最新版本的 Azure Kubernetes 服务，请运行以下命令。 仅当安装了 Oct 版本后，update 命令才起作用。 对于早于10月版本的版本，它将不起作用。 此更新命令将更新 Azure Kubernetes 服务主机和本地 Microsoft 运行的云平台。 对于此预览版本，Kubernetes 版本和 AKS 主机操作系统版本仍保持不变。 此命令不会升级任何现有的工作负荷群集。 更新 AKS 主机后创建的新工作负载群集将不同于 Windows 节点 OS 版本和 Kubernetes 版本中的现有工作负荷群集。

   ```powershell
   Update-AksHci
   ```
   
建议在更新管理群集后立即更新工作负荷群集，以防止在 Kubernetes 群集中运行 Windows 节点不受支持的 Windows Server 操作系统版本。 若要更新工作负荷群集，请访问 [更新工作负荷群集](create-kubernetes-cluster-powershell.md)。

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上重新启动 Azure Kubernetes 服务

如果在 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，则将删除所有 Kubernetes 群集（如果有）以及 Azure Kubernetes 服务主机。 它还会从节点中卸载 Azure Stack HCI 代理和服务上的 Azure Kubernetes 服务。 然后，它会重复原始安装过程步骤，直到重新创建主机。 你通过 `Set-AksHciConfig` 配置的 Azure Stack HCI 上的 Azure Kubernetes 服务配置和下载的 VHDX 映像将保留。

若要在具有相同配置设置的 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，请运行以下命令。

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>在 Azure Stack HCI 上重置配置设置并重新安装 Azure Kubernetes 服务

若要在具有不同配置设置的 Azure Stack HCI 上重新安装 Azure Kubernetes Service，请首先运行以下命令。

```powershell
Uninstall-AksHci
```

运行上述命令后，可以通过以下命令更改配置设置。 参数将保持不变，如步骤3中所述。 如果在未指定参数的情况下运行此命令，则参数将重置为其默认值。

```powershell
Set-AksHciConfig
```

将配置更改为所需的设置后，运行以下命令，在 Azure Stack HCI 上重新安装 Azure Stack Kubernetes。

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>删除 Azure Stack HCI 上的 Azure Kubernetes 服务

若要删除 Azure Stack HCI 上的 Azure Kubernetes 服务，请运行以下命令。 **如果使用 PowerShell 卸载 Windows 管理中心部署，则必须使用标志运行命令 `-Force` 。**

```powershell
Uninstall-AksHci
```

运行上述命令后，可以运行 `Install-AksHci` 命令来安装 Azure Kubernetes 服务主机，使其配置与以前相同。 如果要更改配置，请在 `Set-AksHciConfig` 运行安装命令之前，以要进行的更改运行。

如果不想保留旧配置，请运行以下命令。

```powershell
Uninstall-AksHci -Force
```

如果 PowerShell 命令在以前用于部署 Windows 管理中心的群集上运行，则 PowerShell 模块会检查 Windows 管理中心配置文件是否存在。 Windows 管理中心将 Windows 管理中心配置文件放在所有节点上。 **如果使用卸载命令并返回到 Windows 管理中心，请使用标志运行上述 uninstall 命令 `-Force` 。如果未执行此操作，则 PowerShell 和 Windows 管理中心将不同步。**

## <a name="next-steps"></a>后续步骤

- [为应用程序创建 Kubernetes 群集](create-kubernetes-cluster-powershell.md)
