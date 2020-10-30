---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 900d7ac882a37e229bec6dc916653cf55992cccb
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049659"
---
接下来，创建或续订 TLS 证书，以保护值-添加资源提供程序终结点：

1. 完成 [ (Csr 生成证书签名请求](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) 中的步骤) 资源提供程序的证书续订。 在此处，可以使用 Azure Stack 集线器就绪检查器工具来创建 CSR。 请确保在 "为其他 Azure Stack 中心服务生成证书请求" 步骤中为资源提供程序运行正确的 cmdlet。 例如，用于 `New-AzsHubEventHubsCertificateSigningRequest` 事件中心。 完成后，你将提交生成的。证书颁发机构的请求文件 (CA) 用于新证书。

2. 收到来自 CA 的证书文件后，请完成 [为部署或轮换准备证书](../operator/azure-stack-prepare-pki-certs.md)中的步骤。 再次使用 "就绪检查程序" 工具，处理从 CA 返回的文件。

3. 最后，完成 [验证 Azure Stack 中心 PKI 证书](../operator/azure-stack-validate-pki-certs.md)中的步骤。 再次使用 "准备情况检查程序" 工具，对新证书执行验证测试。


