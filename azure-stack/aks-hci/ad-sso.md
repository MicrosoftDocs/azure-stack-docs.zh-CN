---
title: 在 AKS 中使用 Active Directory 单一登录 Azure Stack HCI
description: 使用 Active Directory Authentication 使用 SSO 凭据安全连接到 API 服务器
author: v-susbo
ms.topic: how-to
ms.date: 02/12/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: fd4b8982ecaa9de1b3b63dd97cb460772e25971d
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873587"
---
# <a name="use-active-directory-single-sign-in-credentials-for-aks-on-azure-stack-hci"></a>Azure Stack HCI 上的 AKS 使用 Active Directory 单一登录凭据

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

如果没有 Active Directory 身份验证，则在通过命令连接到 API 服务器时，用户必须依赖基于证书的 _kubeconfig_ 文件 `kubectl` 。 _Kubeconfig_ 文件包含机密，如需要小心分发的私钥和证书。 这可能会造成严重的安全风险。

作为使用基于证书的 _kubeconfig_ 的替代方法，你可以使用 ACTIVE DIRECTORY (AD) 单一登录 (SSO) 凭据作为连接到 API 服务器的安全方式。 与 Azure Kubernetes Service 在 Azure Stack HCI 上的 AD 集成，可让已加入 Windows 域的计算机上的用户通过 `kubectl`) 使用其 SSO 凭据连接到 API 服务器 (。 这样就不必管理和分发包含私钥的基于证书的 _kubeconfig_ 文件。

AD 集成使用 AD _kubeconfig_，它与基于证书的 _kubeconfig_ 文件不同，不包含任何机密。 但是，如果使用 Active Directory 凭据进行连接时出现问题，则可以使用基于证书的 _kubeconfig_ 文件进行故障排除等备份。

AD 集成的另一个安全优势在于，用户和组作为 [安全标识符存储 (sid) ](https://docs.microsoft.com/troubleshoot/windows-server/identity/security-identifiers-in-windows)。 与组名称不同，Sid 是不可变和唯一的，因此不存在命名冲突。

> [!Note] 
> 对于此预览版，我们只为工作负荷群集提供 AD SSO 连接。 

本文档将指导你完成以下步骤，将 Active Directory 设置为标识提供者，并通过以下步骤启用 SSO `kubectl` ：

- 为 API 服务器创建 AD 帐户，然后创建与该帐户相关联的 [keytab](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) 文件。 请参阅 [使用 keytab 文件创建 Ad 身份验证](#create-ad-auth-using-the-keytab-file) ，创建 ad 帐户并生成 keytab 文件。
- 使用 [keytab](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) 文件在 Kubernetes 群集上安装 AD 身份验证。 作为此步骤的一部分，将自动创建默认的基于角色的访问控制 (RBAC) 配置。
- 创建或编辑 RBAC 配置时，将组名称转换为 Sid，反之亦然。有关说明，请参阅 [创建和更新 AD 组角色绑定](#create-and-update-the-ad-group-role-binding) 。
 
## <a name="before-you-begin"></a>准备阶段

在开始配置 Active Directory SSO 凭据的过程之前，应确保具有以下可用项：

 - 已安装最新的 **Aks-Hci PowerShell** 模块。 如果不这样做，请参阅 [下载并安装 AksHci PowerShell 模块](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module)。 
 - 安装并配置 AKS 主机。 否则，请遵循 [配置部署](./setup-powershell.md#step-4-configure-your-deployment)。  
 - 请确保 keytab 文件可供使用。 如果不是，请参阅 [创建 API 服务器 AD 帐户和 keytab 文件](#create-the-api-server-ad-account-and-the-keytab-file)。 
 - 为 (SPN) 的特定服务主体名称生成 keytab 文件，并且此 SPN 必须对应于工作负荷群集的 API 服务器 AD 帐户。 还应确保在整个 AD 身份验证过程中使用相同的 SPN。 Keytab 文件应命名为 _keytab_。
 - 对于 Azure Stack HCI 工作负载群集上的每个 AKS，请确保有一个可用的 API 服务器 AD 帐户。
 - 客户端计算机必须是已加入 Windows 域的计算机。

## <a name="create-ad-auth-using-the-keytab-file"></a>使用 keytab 文件创建 AD 身份验证

### <a name="step-1-create-the-workload-cluster-and-enable-ad-authentication"></a>步骤1：创建工作负荷群集并启用 AD 身份验证

安装 AD 身份验证之前，必须先在 Azure Stack HCI 工作负载群集上创建一个 AKS，并在群集上启用 AD 身份验证外接程序。 如果在创建新群集时未启用 AD 身份验证，则稍后将无法启用 AD 身份验证。

以管理员身份打开 PowerShell，使用命令的 **-enableADAuth** 参数运行以下 `New-AksHciCluster` 命令：

```powershell
New-AksHciCluster -name mynewcluster1 -enableADAuth
```

对于每个工作负荷群集，请确保有一个可用的 API 服务器 AD 帐户。

有关创建工作负荷群集的详细信息，请参阅 [使用 Windows PowerShell 创建 Kubernetes 群集](./create-kubernetes-cluster-powershell.md)。

### <a name="step-2-install-ad-authentication"></a>步骤2：安装 AD 身份验证

安装 AD 身份验证之前，请确保已安装工作负荷群集并在群集上启用了 AD 身份验证。 若要安装 AD 身份验证，请使用以下选项之一。

#### <a name="option-1"></a>选项 1

对于已加入域的 Azure Stack HCI 群集，请以管理员身份打开 PowerShell 并运行以下命令：

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8s/apiserver@CONTOSO.COM -adminUser contoso\bob
```  

#### <a name="option-2"></a>方法 2

如果群集主机未加入域，请使用 SID 格式的管理员用户名或组名，如以下示例中所示：
 
```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8
```  

若要查找用户帐户的 SID，请参阅 [确定用户或组的安全标识符](#determine-the-user-or-group-security-identifier)。 

在继续执行后续步骤之前，应注意以下事项：

- 请确保 keytab 文件的名称为 _keytab_。
- 替换与你的环境相对应的 SPN。
- **-adminUser** 使用群集管理员权限为指定的 AD 组创建相应的角色绑定。 将 _contoso\bob_ 替换为与你的环境相对应的 AD 组或用户。

### <a name="step-3-test-the-ad-webhook-and-keytab-file"></a>步骤3：测试 AD webhook 和 keytab 文件

你需要确保 AD webhook 在 API 服务器上运行，并且 keytab 文件存储为 Kubernetes 机密。 若要获取工作负荷群集的基于证书的 _kubeconfig_ 文件，请执行以下步骤：

1. 获取基于证书的 _kubeconfig_ 文件。 使用以下命令将 _kubeconfig_ 文件作为本地主机连接到群集：

   ```powershell
   Get-AksHciCredential -name mynewcluster1
   ```  

2. `kubectl`使用之前) 创建的基于证书的 _kubeconfig_ 文件在你连接到 (的服务器上运行，然后检查 ad webhook 部署，以确保其格式为 " _ad auth-webhook-xxxx_"。  

    ```bash
    kubectl get pods -n=kube-system
    ```

3. 运行 `kubectl` 检查是否将 keytab 文件部署为机密并作为 Kubernetes 机密列出： 

   ```bash
   kubectl get secrets -n=kube-system
   ```

### <a name="step-4-create-the-ad-kubeconfig-file"></a>步骤4：创建 AD kubeconfig 文件

成功部署 AD webhook 和 keytab 后，创建 AD _kubeconfig_ 文件。
创建文件后，将 AD _kubeconfig_ 文件复制到客户端计算机，并使用它对 API 服务器进行身份验证。 与基于证书的 _kubeconfig_ 文件不同，AD _kubeconfig_ 不是机密，可以安全地复制为纯文本。

以管理员身份打开 PowerShell 并运行以下命令：  

   ```powershell
   Get-AksHciCredential -name mynewcluster1 -outputLocation .\AdKubeconfig -adAuth
   ```

### <a name="step-5-copy-kubeconfig-and-other-files-to-the-client-machine"></a>步骤5：将 kubeconfig 和其他文件复制到客户端计算机

应将下面列出的三个文件从 Azure Stack HCI 群集复制到客户端计算机：

- 将在上一步中创建的 AD _kubeconfig_ 文件复制到 $ENV： USERPROFILE \. kube\config。

- 创建文件夹路径 `c:\adsso` 并将以下文件从 AZURE STACK HCI 群集复制到客户端计算机。
  - C:\adsso 下 `$env:ProgramFiles\AksHci` 的 Kubectl.exe
  - C:\adsso 下 `$env:ProgramFiles\AksHci` 的 Kubectl-adsso.exe

### <a name="step-6-connect-to-the-api-server-from-the-client-machine"></a>步骤6：从客户端计算机连接到 API 服务器

完成前面的步骤后，使用 SSO 凭据登录到已加入 Windows 域的客户端计算机。 打开 PowerShell，然后尝试使用访问 API 服务器 `kubectl` 。 如果系统提示进行身份验证，则已成功设置 AD SSO。

## <a name="create-and-update-the-ad-group-role-binding"></a>创建和更新 AD 组角色绑定

如步骤2中所述，将为在安装过程中提供的用户和/或组创建具有群集管理员权限的默认角色绑定。 Kubernetes 中的角色绑定定义 AD 组的访问策略。 此步骤介绍如何在 Kubernetes 中使用 RBAC 创建新的 AD 组角色绑定，以及如何编辑现有角色绑定。 例如，群集管理员可能希望使用 AD 组向用户授予额外的特权 (这使该过程更有效) 。 若要了解有关 RBAC 的详细信息，请参阅 [使用 rbac 授权](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。

创建或编辑更多 AD 组 RBAC 条目时，使用者名称应由 "" 预先固定 `microsoft:activedirectory:CONTOSO\\group name` 。 请注意，名称必须包含域名和用双引号引起来的前缀。

这里是两个示例：

**示例 1**

```bash
apiVersion: rbac.authorization.k8s.io/v1 
 kind: ClusterRoleBinding 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: ClusterRole 
   name: cluster-admin 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\Bob" 
```

**示例 2**

下面的示例演示如何为具有 AD 组的 [命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 创建自定义角色和角色绑定。 在此示例中，"SREGroup" 是 Contoso Active Directory 中预先存在的组。 将用户添加到 AD 组后，他们会立即获得权限。

```bash
kind: Role 
 apiVersion: rbac.authorization.k8s.io/v1 
 metadata: 
   name: sre-user-full-access 
   namespace: sre 
 rules: 
 - apiGroups: ["", "extensions", "apps"] 
   resources: ["*"] 
   verbs: ["*"] 
 - apiGroups: ["batch"] 
   resources: 
   - jobs 
   - cronjobs 
   verbs: ["*"] 
 apiVersion: rbac.authorization.k8s.io/v1 
 kind: RoleBinding 
 namespace: sre 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: Role 
   name: sre-user-full-access 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\SREGroup" 
```

在应用 yaml 文件之前，应始终使用以下命令将 **组名和用户名** 转换为 sid：

```bash
kubectl-adsso nametosid <rbac.yml>
```  

同样，若要更新现有 RBAC，可以在进行更改之前将 SID 转换为用户友好的组名。 若要转换 SID，请使用命令： 

```bash
kubectl-adsso sidtoname <rbac.yml>
```

## <a name="change-the-ad-account-password-associated-with-the-api-server-account"></a>更改与 API 服务器帐户关联的 AD 帐户密码

如果为 API 服务器帐户更改了密码，则必须卸载 AD 身份验证外接程序，并使用更新的当前和以前的 keytabs 重新安装它。 

每次更改密码时，都必须将当前的 (keytab) " _keytab_ " 重命名为 " _keytab_"，然后确保将新密码命名为 " _keytab_"。

> [!Important] 
> 文件必须分别命名为 " _keytab_ " 和 " _keytab_"。 现有角色绑定不受此影响。

### <a name="uninstall-and-reinstall-ad-authentication"></a>卸载并重新安装 AD 身份验证

如果更改了 API 服务器的帐户、更新了密码或解决了失败，则可能需要重新安装 AD SSO。

若要卸载 AD 身份验证，请以管理员身份打开 PowerShell 并运行以下命令：  

```powershell
Uninstall-AksHciAdAuth -name mynewcluster1
```

若要重新安装 AD 身份验证，请以管理员身份打开 PowerShell 并运行以下命令：

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

> [!Note] 
> `-previousKeytab`仅当更改密码时，才需要参数。 这是为了避免客户端具有缓存票证时的停机时间。

## <a name="create-the-api-server-ad-account-and-the-keytab-file"></a>创建 API 服务器 AD 帐户和 keytab 文件

此过程涉及两个步骤。 首先，为使用服务主体名称 (SPN) 和秒的 API 服务器创建一个新的 AD 帐户/用户，为 AD 帐户创建 keytab 文件。

### <a name="step-1-create-a-new-ad-account-or-user-for-the-api-server"></a>步骤1：为 API 服务器创建新的 AD 帐户或用户

使用 [New-aduser](https://docs.microsoft.com/powershell/module/addsadministration/new-aduser?view=win10-ps&preserve-view=true) PowerShell 命令使用 SPN 创建新的 AD 帐户/用户。 下面是一个示例： 

```powershell 
New-ADUser -Name apiserver -ServicePrincipalNames k8s/apiserver -AccountPassword (ConvertTo-SecureString "password" -AsPlainText -Force) -KerberosEncryptionType AES128 -Enabled 1
```

### <a name="step-2-create-the-keytab-file-for-the-ad-account"></a>步骤2：创建 AD 帐户的 keytab 文件

若要创建 keytab 文件，请使用 [ktpass](https://docs.microsoft.com/windows-server/administration/windows-commands/ktpass) Windows 命令。 

下面是一个使用 ktpass 的示例：

```bash
ktpass /out current.keytab /princ k8s/apiserver@BCONTOSO.COM /mapuser contoso\apiserver_acct /crypto all /pass p@$$w0rd /ptype KRB5_NT_PRINCIPAL
```

> [!NOTE]
> 如果看到此错误， **DsCrackNames 在 name 条目中返回 0x2**，请确保的参数的 `/mapuser` 格式为 `mapuser DOMAIN\user` DOMAIN，其中域是回显的输出 `%userdomain%` 。

  
## <a name="determine-the-user-or-group-security-identifier"></a>确定用户或组的安全标识符

使用以下两个选项之一来查找你的帐户或其他帐户的 SID。

若要查找与你的帐户关联的 SID，请在主目录的命令提示符下键入以下内容：

```bash
whoami /user
``` 

若要查找与其他帐户关联的 SID，请以管理员身份打开 PowerShell 并运行以下内容：

```powershell
(New-Object System.Security.Principal.NTAccount(<CONTOSO\Bob>)).Translate([System.Security.Principal.SecurityIdentifier]).value
```

## <a name="next-steps"></a>后续步骤 

本操作方法指南介绍了如何配置 AD 身份验证，以安全地连接到带有 SSO 凭据的 API 服务器。 接下来可以：

- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows Server 应用程序](./deploy-windows-application.md)。
