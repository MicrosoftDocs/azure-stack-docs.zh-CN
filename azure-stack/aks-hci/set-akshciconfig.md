---
title: Set-AksHciConfig
author: jessicaguan
description: Set-AksHciConfig PowerShell 命令将更新 Azure Kubernetes 服务主机的配置设置。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 80a6fc50bbfcaf028d6c810bbef7e2d6b4508cab
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873629"
---
# <a name="set-akshciconfig"></a>Set-AksHciConfig

## <a name="synopsis"></a>摘要
设置或更新 Azure Kubernetes 服务主机的配置设置。

## <a name="syntax"></a>语法

### <a name="set-configuration-for-host"></a>设置主机的配置
```powershell
Set-AksHciConfig [-imageDir <String>]
                 [-cloudConfigLocation <String>]
                 [-nodeConfigLocation <String>]
                 [-vnet <Virtual Network>]
                 [-controlPlaneVmSize <VmSize>]
                 [-sshPublicKey <String>]
                 [-macPoolStart <String>]
                 [-macPoolEnd <String>]       
                 [-proxyServerHTTP <String>]
                 [-proxyServerHTTPS <String>]
                 [-proxyServerNoProxy <String>]
                 [-proxyServerCertFile <String>]
                 [-proxyServerCredential <PSCredential>]
                 [-cloudServiceCidr <String>]
                 [-workingDir <String>]
                 [-version <String>]
                 [-nodeAgentPort <int>]
                 [-nodeAgentAuthorizerPort <int>]
                 [-clusterRoleName <String>]
                 [-cloudLocation <String>]
                 [-skipHostLimitChecks]
                 [-insecure]
                 [-skipUpdates]
                 [-forceDnsReplication]
                 [-enableDiagnosticData]   
```

## <a name="description"></a>说明
设置 Azure Kubernetes 服务主机的配置设置。 如果要在2-4 节点 Azure Stack HCI 群集或 Windows Server 2019 Datacenter 故障转移群集，则必须指定 imageDir 和 cloudConfigLocation 参数。 对于单节点 Windows Server 2019 Datacenter，所有参数都是可选的，并设置为其默认值。 但是，为了获得最佳性能，建议使用 2-4 节点的 Azure Stack HCI 群集部署。

## <a name="examples"></a>示例

### <a name="to-deploy-on-a-2-4-node-cluster-with-dhcp-networking"></a>使用 DHCP 网络部署在2-4 节点群集上
```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
```

### <a name="to-deploy-with-a-virtual-ip-pool"></a>使用虚拟 IP 池进行部署
```powershell
PS C:\> New-AksHciNetworkSetting -name newNetwork -subnetCidr 192.168.2.0/32 -defaultGateway 192.168.2.1 -subnetVSwitch External -vipPoolStart 192.168.2.20 -vipPoolEnd 192.168.2.80   
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -networkSettings newNetwork
```

### <a name="to-deploy-with-a-proxy-server"></a>使用代理服务器进行部署
```powershell
PS C:\> $credential = Get-Credential
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
```

## <a name="parameters"></a>参数

### <a name="-imagedir"></a>-imageDir
Azure Stack HCI 上的 Azure Kubernetes 服务将在其中存储 VHD 映像的目录的路径。 对于单节点部署，默认值为 `%systemdrive%\AksHciImageStore`。 对于多节点部署，必须指定此参数。 路径必须指向共享的存储路径（例如 `C:\ClusterStorage\Volume2\ImageStore` ）或 SMB 共享（例如 `\\FileShare\ImageStore`）。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\AksHciImageStore
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudconfiglocation"></a>-cloudConfigLocation
云代理将存储其配置的位置。 对于单节点部署，默认值为 `%systemdrive%\wssdcloudagent`。 位置可以与上面的 imageDir 路径相同。对于多节点部署，必须指定此参数。 路径必须指向共享的存储路径（例如 `C:\ClusterStorage\Volume2\ImageStore` ）或 SMB 共享（例如 `\\FileShare\ImageStore`）。 此位置需要在高度可用的共享上，以便始终可以访问该存储。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\wssdcloudagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeconfiglocation"></a>-nodeConfigLocation
节点代理将存储其配置的位置。 每个节点都有一个节点代理，因此其配置在本地。 此位置必须是本地路径。 对于所有部署，默认值为 `%systemdrive%\programdata\wssdagent` 。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\programdata\wssdagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>-vnet
New-AksHciNetworkSetting 命令创建的 AksHciNetworkSetting 对象的名称。
```yaml
Type: VirtualNetwork
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
要为控制平面创建的 VM 的大小。 默认值为 Standard_A4_V2。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-sshpublickey"></a>-sshPublicKey
SSH 公钥文件的路径。 使用此公钥，你将能够登录到 Azure Stack HCI 部署上的 Azure Kubernetes 服务创建的任何 VM。 如果你有自己的 SSH 公钥，你将在此处传递其位置。 如果未提供任何密钥，我们将在 .pub 下查找一个。 `%systemdrive%\akshci\.ssh\akshci_rsa` 如果该文件不存在，将生成并使用上述位置的 SSH 密钥对。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolstart"></a>-macPoolStart
这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址开头。 MAC 地址的语法要求第一个字节的最小有效位应始终为0，第一个字节应始终为偶数 (，即00，02，04，06 ... ) 。典型的 MAC 地址如下所示：02：1E：2B：78：00：00。 将 MAC 池用于长期部署，以便分配的 MAC 地址保持一致。 如果要求 Vm 具有特定的 MAC 地址，这会很有用。 默认为无。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolend"></a>-macPoolEnd
这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址结尾。 MAC 地址的语法要求第一个字节的最小有效位应始终为0，第一个字节应始终为偶数 (，即00，02，04，06 ... ) 。作为-macPoolEnd 传递的地址的第一个字节应与作为-macPoolStart 传递的地址的第一个字节相同。 将 MAC 池用于长期部署，以便分配的 MAC 地址保持一致。 如果要求 Vm 具有特定的 MAC 地址，这会很有用。 默认为无。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttp"></a>-proxyServerHTTP
这会提供一个代理服务器 URI，该 URI 应由需要访问 HTTP 终结点的所有组件使用。 URI 格式包括 URI 架构、服务器地址和端口 (，即 https://server.com:8888) 。 默认为无。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttps"></a>-proxyServerHTTPS
这会提供一个代理服务器 URI，该 URI 应由需要访问 HTTPS 终结点的所有组件使用。 URI 格式包括 URI 架构、服务器地址和端口 (，即 https://server.com:8888) 。 默认为无。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservernoproxy"></a>-proxyServerNoProxy
这是将从代理中免除的地址的逗号分隔字符串。 默认值为 localhost、127.0.0.1、.svc、10.96.0.0/12、10.244.0.0/16。 这不包括来自代理服务器的本地主机流量 (localhost、127.0.0.1) 、内部 Kubernetes 服务流量 ( .svc) 、Kubernetes Service CIDR (10.96.0.0/12) 和 Kubernetes POD CIDR (10.244.0.0/16) 。 可以使用此参数添加更多子网范围或名称免除。 **此参数的设置非常重要，因为如果未正确配置，则可能会意外地将内部 Kubernetes 群集流量路由到代理。这可能导致网络通信失败。**

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
用于向代理服务器进行身份验证的证书。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:
 
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercredential"></a>-proxyServerCredential
这会提供用户名和密码以向 HTTP/HTTPS 代理服务器进行身份验证。 你可以使用 `Get-Credential` 生成可传递给此参数的 PSCredential 对象。 默认为无。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudservicecidr"></a>-cloudServiceCidr
这可用于提供一个静态 IP/网络前缀来分配给 MOC CloudAgent 服务。 应使用 CIDR 格式提供此值。 （示例：192.168.1.2/16）。 你可能需要指定此项以确保始终可以访问网络上的任何重要内容，因为 IP 地址不会更改。 默认为无。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-workingdir"></a>-workingDir
这是用于存储小文件的模块的工作目录。 默认为 `%PROGRAMFILES%\AksHci` ，大多数部署不应进行更改。建议不要更改默认值。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %PROGRAMFILES%\AksHci
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-version"></a>-version
要部署的 Azure Stack HCI 上的 Azure Kubernetes 服务的版本。 默认值为最新版本。 建议不要更改默认值。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Latest version
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentport"></a>-nodeAgentPort
节点代理应侦听的 TCP/IP 端口号。 默认为 45000。建议不要更改默认值。

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45000
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentauthorizerport"></a>-nodeAgentAuthorizerPort
节点代理为授权端口使用的 TCP/IP 端口号。 默认为 45001。 建议不要更改默认值。 

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45001
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clusterrolename"></a>-clusterRoleName
此名称指定在将云代理创建为群集内的通用服务时要使用的名称。 此名称默认为具有 ca 前缀的唯一名称和 guid 后缀 (例如： "9e6eb299-bc0b-4f00-9fd7-942843820c26" ) 。 建议不要更改默认值。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: A unique name with a prefix of ca- and a guid suffix
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudlocation"></a>-cloudLocation
此参数提供自定义的 Microsoft 操作云位置名称。 默认名称为 "MocLocation"。 建议不要更改默认值。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: MocLocation
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skiphostlimitchecks"></a>-skipHostLimitChecks
在允许部署继续进行之前，请求脚本跳过它用于确认内存和磁盘空间是否可用的所有检查。 不建议使用此设置。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-insecure"></a>-不安全
在 Azure Stack HCI 组件（例如云代理和节点代理）上部署 Azure Kubernetes 服务，) 在不安全模式下 (s， (不会) TLS 安全连接。不建议在生产环境中使用不安全模式。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipupdates"></a>-skipUpdates
如果要跳过任何可用的更新，请使用此标志。 不建议使用此设置。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-forcednsreplication"></a>-forceDnsReplication
在某些系统上，DNS 复制最多可能需要一小时。 这会导致部署速度缓慢。 如果遇到此问题，你会看到 Install-AksHci 停滞在循环中。 若要解决此问题，请尝试使用此标志。 -ForceDnsReplication 标志不是可保证的修补程序。 如果该标志背后的逻辑失败，则会隐藏该错误，并且命令将继续执行，如同未提供该标志一样。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablediagnosticdata"></a>-enableDiagnosticData'

使用此标志同意将所需的诊断数据发送给 Microsoft。 如果要跳过将显示的同意提示，此方法非常有用 `Set-AksHciConfig` 。 `Install-AksHci` 如果你不接受许可提示，则会失败 `Set-AksHciConfig` 。
