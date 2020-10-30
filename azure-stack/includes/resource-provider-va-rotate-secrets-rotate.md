---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: ff18909ef7586d8099f0e01f29d53f3dbc3677f1
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049661"
---
最后，确定资源提供程序的最新部署属性，并使用它们来完成机密轮换过程。

### <a name="determine-deployment-properties"></a>确定部署属性

资源提供程序作为版本的产品包部署到 Azure Stack 中心环境。 为包分配一个唯一的包 ID，格式为 `'<product-id>.<installed-version>'` 。 其中， `<product-id>` 是表示资源提供程序的唯一字符串， `<installed-version>` 表示特定版本。 与每个包相关联的机密存储在 Azure Stack 中心 Key Vault 服务中。 

打开提升了权限的 PowerShell 控制台并完成以下步骤，以确定轮换资源提供程序的机密所需的属性：

1. 使用操作员凭据登录到 Azure Stack 中心环境。 请参阅 [通过 Powershell 连接到 Azure Stack 集线器](../operator/azure-stack-powershell-configure-admin.md) 以获取 powershell 登录脚本。 请确保使用 PowerShell Az cmdlet (而不是 AzureRM) ，并替换所有占位符值，例如终结点 Url 和目录租户名称。

2. 运行 `Get-AzsProductDeployment` cmdlet 以检索最新资源提供程序部署的列表。 `"value"`对于每个已部署的资源提供程序，返回的集合都包含一个元素。 找到相关的资源提供程序，记下这些属性的值：
   - `"name"` -包含值的第二个段中的资源提供程序产品 ID。 
   - `"properties"."deployment"."version"` -包含当前部署的版本号。 

   在以下示例中，请注意集合中第一个元素的事件中心 RP 部署，该元素的产品 ID 为 `"microsoft.eventhub"` ，版本为 `"1.2003.0.0"` ：

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductDeployment -AsJson
   VERBOSE: GET https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 2656-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments/microsoft.eventhub",
                         "name":  "global/microsoft.eventhub",
                         "type":  "Microsoft.Deployment.Admin/locations/productDeployments",
                         "properties":  {
                                            "status":  "DeploymentSucceeded",
                                            "subscriptionId":  "b37ae55a-a6c6-4474-ba97-81519412adf5",
                                            "deployment":  {
                                                               "version":  "1.2003.0.0",
                                                               "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                               "parameters":  {
   
                                                                              }
                                                           },
                                            "lastSuccessfulDeployment":  {
                                                                             "version":  "1.2003.0.0",
                                                                             "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                                             "parameters":  {
   
                                                                                            }
                                                                         },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     {
                     ...
                     }
                 ]
   }
   ```

3. 通过连接资源提供程序产品 ID 和版本来生成资源提供程序的包 ID。 例如，使用上一步中派生的值，事件中心 RP 包 ID 是 `microsoft.eventhub.1.2003.0.0` 。 

4. 使用上一步中派生的包 ID，运行 `Get-AzsProductSecret -PackageId` 以检索资源提供程序所使用的机密类型的列表。 在返回的 `value` 集合中，查找包含属性值的元素 `"Certificate"` `"properties"."secretKind"` 。 此元素包含 RP 证书密钥的属性。 记下分配给此证书机密的名称，该名称由属性的最后一段标识 `"name"` ，就像上面的属性的最后一个段 `"properties"` 。 

   在以下示例中，为事件中心 RP 返回的机密集合包含 `"Certificate"` 名为的机密 `aseh-ssl-gateway-pfx` 。 

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductSecret -PackageId 'microsoft.eventhub.1.2003.0.0' -AsJson
   VERBOSE: GET
   https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 617-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets/aseh-ssl-gateway-pfx",
                         "name":  "global/microsoft.eventhub.1.2003.0.0/aseh-ssl-gateway-pfx",
                         "type":  "Microsoft.Deployment.Admin/locations/productPackages/secrets",
                         "properties":  {
                                            "secretKind":  "Certificate",
                                            "description":  "Event Hubs gateway SSL certificate.",
                                            "expiresAfter":  "P730D",
                                            "secretDescriptor":  {
   
                                                                 },
                                            "secretState":  {
                                                                "status":  "Deployed",
                                                                "rotationStatus":  "None",
                                                                "expirationDate":  "2022-03-31T00:16:05.3068718Z"
                                                            },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     ...
                 ]
   }
   ```

### <a name="rotate-the-secrets"></a>旋转机密

1. 使用 `Set-AzsProductSecret` cmdlet 将新证书导入到 Key Vault，旋转过程将使用该证书。 在运行脚本之前，请相应地替换变量占位符值：

   | 占位符 | 说明 | 示例值 |
   | ----------- | ----------- | --------------|
   | `<product-id>` | 最新资源提供程序部署的产品 ID。 | `microsoft.eventhub` |
   | `<installed-version>` | 最新资源提供程序部署的版本。 | `1.2003.0.0` |
   | `<cert-secret-name>` | 用于存储证书机密的名称。 | `aseh-ssl-gateway-pfx` |
   | `<cert-pfx-file-path>` | 证书 PFX 文件的路径。 | `C:\dir\eh-cert-file.pfx` |
   | `<pfx-password>` | 分配给证书的密码。PFX 文件。 | `strong@CertSecret6` |

   ```powershell
   $productId = '<product-id>'
   $packageId = $productId + '.' + '<installed-version>'
   $certSecretName = '<cert-secret-name>' 
   $pfxFilePath = '<cert-pfx-file-path>'
   $pfxPassword = ConvertTo-SecureString '<pfx-password>' -AsPlainText -Force   
   Set-AzsProductSecret -PackageId $packageId -SecretName $certSecretName -PfxFileName $pfxFilePath -PfxPassword $pfxPassword -Force
   ```

2. 最后，使用 `Invoke-AzsProductRotateSecretsAction` cmdlet 来旋转内部和外部机密：

   > [!NOTE]
   > 完成轮换过程大约需要3.5 到4小时。

   ```powershell
   Invoke-AzsProductRotateSecretsAction -ProductId $productId
   ```
   
   可以通过在 "Marketplace 服务" 中选择资源提供程序，在 PowerShell 控制台或管理员门户中监视机密旋转进度：

   [![秘密旋转-进度](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png)](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png#lightbox)

