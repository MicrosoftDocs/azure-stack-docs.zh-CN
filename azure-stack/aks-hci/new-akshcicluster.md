---
title: New-AksHciCluster
author: jessicaguan
description: New-AksHciCluster PowerShell 命令将创建新的托管 Kubernetes 群集。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d4b374ca093c60018d1dc68c3d5a39db68456298
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873594"
---
# <a name="new-akshcicluster"></a>New-AksHciCluster

## <a name="synopsis"></a>摘要
创建新的托管 Kubernetes 群集。

## <a name="syntax"></a>语法

```powershell
New-AksHciCluster -name <String>
                 [-kubernetesVersion <String>]
                 [-controlPlaneNodeCount <int>]
                 [-linuxNodeCount <int>]
                 [-windowsNodeCount <int>]
                 [-controlPlaneVmSize <VmSize>]
                 [-loadBalancerVmSize <VmSize>]
                 [-linuxNodeVmSize <VmSize>]
                 [-windowsNodeVmSize <VmSize>]
                 [-vnet <Virtual Network>]
                 [-primaryNetworkPlugin <Network Plugin>]   
                 [-enableAdAuth]
                 [-enableSecretsEncryption]          
```

## <a name="description"></a>说明

在 Azure Stack HCI 群集上创建新的 Azure Kubernetes 服务。

## <a name="examples"></a>示例

### <a name="new-aks-hci-cluster-with-required-params"></a>具有必需的参数的新 AKS-HCI 群集。

```powershell
PS C:\> New-AksHciCluster -name myCluster
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-name
Kubernetes 群集的名称。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-kubernetesversion"></a>-kubernetesVersion
要部署的 Kubernetes 的版本。 默认值为最新版本。 若要获取可用版本的列表，请运行 `Get-AksHciKubernetesVersion`。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: v1.18.8 or later
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
控制平面中的节点数。 默认值为 1。

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Kubernetes 群集中的 Linux 节点数。 默认值为 1。

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsnodecount"></a>-windowsNodeCount
Kubernetes 群集中的 Windows 节点数。 默认为 0。 如果运行的是 Kubernetes v 1.18.8 或更高版本，则只能部署 Windows 节点。

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
控制平面 VM 的大小。 默认值为 Standard_A4_V2。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

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

### <a name="-loadbalancervmsize"></a>-loadBalancerVmSize
负载均衡器 VM 的大小。 默认值为 Standard_A4_V2。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

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

### <a name="-linuxnodevmsize"></a>-linuxNodeVmSize
Linux 节点 VM 的大小。 默认值为 Standard_K8S3_v1。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsvmsize"></a>-windowsVmSize
Windows 节点 VM 的大小。 默认值为 Standard_K8S3_v1。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>-vnet
New-AksHciNetworkSetting 命令创建的 AksHciNetworkSetting 对象的名称。

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

### <a name="-primarynetworkplugin"></a>-primaryNetworkPlugin
要用于部署的网络插件。 此参数采用 `flannel` 或 `calico` 。 Calico 仅适用于仅限 Linux 的群集。 如果为群集选择 Calico，则不允许添加 Windows 节点。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: flannel
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enableadauth"></a>-enableADAuth
使用此标志可在 Kubernetes 群集中启用 Active Directory。

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablesecretsencryption"></a>-enableSecretsEncryption
使用此标志可在 Kubernetes 机密中启用加密。

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
