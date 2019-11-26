---
title: Azure Stack known issues
description: Learn about known issues in Azure Stack releases.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 0621ca19f96e1c33a85ea5308346fafa2e4545f8
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465365"
---
# <a name="azure-stack-known-issues"></a>Azure Stack known issues

This article lists known issues in releases of Azure Stack. The list is updated as new issues are identified.

To access known issues for a different version, use the version selector dropdown above the table of contents on the left.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Review this section before applying the update.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> If your Azure Stack instance is behind by more than two updates, it's considered out of compliance. You must [update to at least the minimum supported version to receive support](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1910"
## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>Administrative subscriptions

- Applicable: This issue applies to all supported releases.
- Cause: The two administrative subscriptions that were introduced with version 1804 should not be used. The subscription types are **Metering** subscription, and **Consumption** subscription.
- Remediation: If you have resources running on these two subscriptions, recreate them in user subscriptions.
- Occurrence: Common

### <a name="subscriptions-lock-blade"></a>Subscriptions Lock blade

- Applicable: This issue applies to all supported releases.
- Cause: In the administrator portal, the **Lock** blade for user subscriptions has two buttons that say **Subscription**.
- Occurrence: Common

### <a name="subscription-permissions"></a>Subscription permissions

- Applicable: This issue applies to all supported releases.
- Cause: You cannot view permissions to your subscription using the Azure Stack portals.
- Remediation: Use [PowerShell to verify permissions](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence: Common

### <a name="storage-account-settings"></a>存储帐户设置

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the storage account **Configuration** blade shows an option to change **security transfer type**. The feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="upload-blob-with-oauth-error"></a>Upload blob with OAuth error

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you try to upload a blob using the **OAuth(preview)** option, the task fails with an error message.
- Remediation: Upload the blob using the SAS option.
- Occurrence: Common

### <a name="upload-blob-option-unsupported"></a>Upload blob option unsupported

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you try to upload a blob in the upload blade, there is an option to select **AAD** or **Key Authentication**, however **AAD** is not supported in Azure Stack.
- Occurrence: Common

### <a name="load-balancer-backend-pool"></a>Load balancer backend pool

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when adding a **Load balancer** backend pool, the operation results in an error message of **Failed to save load balancer backend pool**; however, the operation did actually succeed.
- Occurrence: Common

### <a name="incorrect-tooltip-when-creating-vm"></a>Incorrect tooltip when creating VM

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you select a managed disk, with disk type Premium SSD, the drop-down list shows **OS Disk**. The tooltip next to that option says **Certain OS Disk sizes may be available for free with Azure Free Account**; however, this is not valid for Azure Stack. In addition, the list includes **Free account eligible** which is also not valid for Azure Stack.
- Occurrence: Common

### <a name="vpn-troubleshoot-and-metrics"></a>VPN troubleshoot and metrics

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **VPN Troubleshoot** feature and **Metrics** in a VPN gateway resource appears, however this is not supported in Azure Stack.
- Occurrence: Common

### <a name="adding-extension-to-vm-scale-set"></a>Adding extension to VM Scale Set

- Applicable: This issue applies to releases 1907 and later.
- Cause: In the user portal, once a virtual machine scale set is created, the UI does not permit the user to add an extension.
- Occurrence: Common

### <a name="delete-a-storage-container"></a>Delete a storage container

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when a user attempts to delete a storage container, the operation fails when the user does not toggle **Override Azure Policy and RBAC Role settings**.
- Remediation: Ensure that the box is checked for **Override Azure Policy and RBAC Role settings**.
- Occurrence: Common

### <a name="refresh-button-on-virtual-machines-fails"></a>Refresh button on virtual machines fails

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you navigate to **Virtual Machines** and try to refresh using the button at the top, the states fail to update accurately.
- Remediation: The status is automatically updated every 5 minutes regardless of whether the refresh button has been clicked or not. Wait 5 minutes and check the status.
- Occurrence: Common

### <a name="virtual-network-gateway"></a>虚拟网络网关

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you create a route table, **Virtual Network gateway** appears as one of the next hop type options; however, this is not supported in Azure Stack.
- Occurrence: Common

### <a name="storage-account-options"></a>存储帐户选项

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the name of storage accounts is shown as **Storage account - blob, file, table, queue**, however **file** is not supported in Azure Stack.
- Occurrence: Common

### <a name="storage-account-configuration"></a>存储帐户配置

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you create a storage account and view its **Configuration**, you cannot save configuration changes, as it results in an AJAX error.
- Occurrence: Common

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>Capacity monitoring in SQL resource provider keeps loading

- Applicable: This issue applies to the Azure Stack 1910 update or later, with SQL resource provider version 1.1.33.0 or earlier installed.
- Cause: The current version of the SQL resource provider is not compatible with some of the latest portal changes in the 1910 update.
- Remediation: Follow the resource provider update process to apply the SQL resource provider hotfix 1.1.47.0 after Azure Stack is upgraded to the 1910 update ([SQL RP version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)). For the MySQL resource provider, it is also recommended that you apply the MySQL resource provider hotfix 1.1.47.0 after Azure Stack is upgraded to 1910 update ([MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)).
- Occurrence: Common

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡

- Applicable: This issue applies to all supported releases.
- Cause: When adding availability set VMs to the backend pool of a load balancer, an error message is displayed on the portal stating **Failed to save load balancer backend pool**. This is a cosmetic issue on the portal; the functionality is still in place and VMs are successfully added to the backend pool internally.
- Occurrence: Common

### <a name="network-security-groups"></a>网络安全组

- Applicable: This issue applies to all supported releases. 
- Cause: An explicit **DenyAllOutbound** rule cannot be created in an NSG as this will prevent all internal communication to infrastructure needed for the VM deployment to complete.
- Occurrence: Common

### <a name="service-endpoints"></a>服务终结点

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network** blade shows an option to use **Service Endpoints**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="network-interface"></a>网络接口

#### <a name="addingremoving-network-interface"></a>Adding/removing network interface

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding or removing a network interface.
- Occurrence: Common

#### <a name="primary-network-interface"></a>主网络接口

- Applicable: This issue applies to all supported releases.
- Cause: The primary NIC of a VM cannot be changed. Deleting or detaching the primary NIC results in issues when starting up the VM.
- Occurrence: Common

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network Gateway** blade shows an option to use **Alerts**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="active-active"></a>主动-主动

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, while creating, and in the resource menu of **Virtual Network Gateway**, you will see an option to enable **Active-Active** configuration. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="vpn-troubleshooter"></a>VPN troubleshooter

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Connections** blade displays a feature called **VPN Troubleshooter**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="documentation"></a>文档

- Applicable: This issue applies to all supported releases.
- Cause: The documentation links in the overview page of Virtual Network gateway link to Azure-specific documentation instead of Azure Stack. Use the following links for the Azure Stack documentation:

  - [Gateway SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Highly Available Connections](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configure BGP on Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute circuits](azure-stack-connect-expressroute.md)
  - [Specify custom IPsec/IKE policies](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM boot diagnostics

- Applicable: This issue applies to all supported releases.
- Cause: When creating a new Windows virtual machine (VM), the following error might be displayed: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** . The error occurs if you enable boot diagnostics on a VM, but delete your boot diagnostics storage account.
- Remediation: Recreate the storage account with the same name you used previously.
- Occurrence: Common

### <a name="consumed-compute-quota"></a>Consumed compute quota

- Applicable: This issue applies to all supported releases.
- When creating a new virtual machine, you may receive an error such as **This subscription is at capacity for Total Regional vCPUs on this location. This subscription is using all 50 Total Regional vCPUs available.** . This indicates that the quota for total cores available to you has been reached.
- Remediation: Ask your operator for an add-on plan with additional quota. Editing the current plan's quota will not work or reflect increased quota.
- Occurrence: Rare

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Create failures during patch and update on 4-node Azure Stack environments

- Applicable: This issue applies to all supported releases.
- Cause: Creating VMs in an availability set of 3 fault domains and creating a virtual machine scale set instance fails with a **FabricVmPlacementErrorUnsupportedFaultDomainSize** error during the update process on a 4-node Azure Stack environment.
- Remediation: You can create single VMs in an availability set with 2 fault domains successfully. However, scale set instance creation is still not available during the update process on a 4-node Azure Stack deployment.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908 update process

- Applicable: This issue applies to all supported releases.
- Cause: When attempting to install the Azure Stack update, the status for the update might fail and change state to **PreparationFailed**. This is caused by the update resource provider (URP) being unable to properly transfer the files from the storage container to an internal infrastructure share for processing.
- Remediation: Starting with version 1901 (1.1901.0.95), you can work around this issue by clicking **Update now** again (not **Resume**). The URP then cleans up the files from the previous attempt, and restarts the download. If the problem persists, we recommend manually uploading the update package by following the [Install updates section](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
- Occurrence: Common

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>Administrative subscriptions

- Applicable: This issue applies to all supported releases.
- Cause: The two administrative subscriptions that were introduced with version 1804 should not be used. The subscription types are **Metering** subscription, and **Consumption** subscription.
- Remediation: If you have resources running on these two subscriptions, recreate them in user subscriptions.
- Occurrence: Common

### <a name="subscriptions-properties-blade"></a>Subscriptions Properties blade

- Applicable: This issue applies to all supported releases.
- Cause: In the administrator portal, the **Properties** blade for subscriptions does not load correctly
- Remediation: You can view these subscription properties in the **Essentials** pane of the **Subscriptions Overview** blade.
- Occurrence: Common

### <a name="subscriptions-lock-blade"></a>Subscriptions Lock blade

- Applicable: This issue applies to all supported releases.
- Cause: In the administrator portal, the **Lock** blade for user subscriptions has two buttons labeled **subscription**.
- Occurrence: Common

### <a name="subscription-permissions"></a>Subscription permissions

- Applicable: This issue applies to all supported releases.
- Cause: You cannot view permissions to your subscription using the Azure Stack portals.
- Remediation: Use [PowerShell to verify permissions](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence: Common

### <a name="storage-account-settings"></a>存储帐户设置

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the storage account **Configuration** blade shows an option to change **security transfer type**. The feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="upload-blob"></a>上传 blob

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you try to upload a blob using the **OAuth(preview)** option, the task fails with an error message.
- Remediation: Upload the blob using the SAS option.
- Occurrence: Common

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

- Applicable: This issue applies to all supported releases. 
- Cause: When adding Avaiability Set VMs to the backend pool of a Load Balancer, an error message is being displayed on the portal stating **Failed to save load balancer backend pool**. This is a cosmetic issue on the portal, the functionality is still in place and VMs are successfully added to the backend pool interally. 
- Occurrence: Common

### <a name="network-security-groups"></a>网络安全组

- Applicable: This issue applies to all supported releases. 
- Cause: An explicit **DenyAllOutbound** rule cannot be created in an NSG as this will prevent all internal communication to infrastructure needed for the VM deployment to complete.
- Occurrence: Common

### <a name="service-endpoints"></a>服务终结点

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network** blade shows an option to use **Service Endpoints**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="network-interface"></a>网络接口

#### <a name="addingremoving-network-interface"></a>Adding/Removing Network Interface

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding/removing a network interface.
- Occurrence: Common

#### <a name="primary-network-interface"></a>Primary Network Interface

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding/removing a network interface.
- Occurrence: Common

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network Gateway** blade shows an option to use **Alerts**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="active-active"></a>主动-主动

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, while creating, and in the resource menu of **Virtual Network Gateway**, you will see an option to enable **Active-Active** configuration. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="vpn-troubleshooter"></a>VPN troubleshooter

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Connections** blade shows a feature called **VPN Troubleshooter**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="documentation"></a>文档

- Applicable: This issue applies to all supported releases.
- Cause: The documentation links in the overview page of Virtual Network gateway link to Azure-specific documentation instead of Azure Stack. Use the following links for the Azure Stack documentation:

  - [Gateway SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Highly Available Connections](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configure BGP on Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute circuits](azure-stack-connect-expressroute.md)
  - [Specify custom IPsec/IKE policies](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM boot diagnostics

- Applicable: This issue applies to all supported releases.
- Cause: When creating a new Windows virtual machine (VM), the following error may be displayed: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** . The error occurs if you enable boot diagnostics on a VM, but delete your boot diagnostics storage account.
- Remediation: Recreate the storage account with the same name you used previously.
- Occurrence: Common

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Create failures during patch and update on 4-node Azure Stack environments

- Applicable: This issue applies to all supported releases.
- Cause: Creating VMs in an availability set of 3 fault domains and creating a virtual machine scale set instance fails with a **FabricVmPlacementErrorUnsupportedFaultDomainSize** error during the update process on a 4-node Azure Stack environment.
- Remediation: You can create single VMs in an availability set with 2 fault domains successfully. However, scale set instance creation is still not available during the update process on a 4-node Azure Stack.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH access

- Applicable: This issue applies to all supported releases.
- Cause: An Ubuntu 18.04 VM created with SSH authorization enabled does not allow you to use the SSH keys to sign in.
- Remediation: Use VM access for the Linux extension to implement SSH keys after provisioning, or use password-based authentication.
- Occurrence: Common

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Virtual machine scale set reset password does not work

- Applicable: This issue applies to all supported releases.
- Cause: A new reset password blade appears in the scale set UI, but Azure Stack does not support resetting password on a scale set yet.
- Remediation: None.
- Occurrence: Common

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Rainy cloud on scale set diagnostics

- Applicable: This issue applies to all supported releases.
- Cause: The virtual machine scale set overview page shows an empty chart. Clicking on the empty chart opens a "rainy cloud" blade. This is the chart for scale set diagnostic information, such as CPU percentage, and is not a feature supported in the current Azure Stack build.
- Remediation: None.
- Occurrence: Common

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtual machine diagnostic settings blade

- Applicable: This issue applies to all supported releases.    
- Cause: The virtual machine diagnostic settings blade has a **Sink** tab, which asks for an **Application Insight Account**. This is the result of a new blade and is not yet supported in Azure Stack.
- Remediation: None.
- Occurrence: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>1907 update process

- Applicable: This issue applies to all supported releases.
- Cause: When attempting to install the 1907 Azure Stack update, the status for the update might fail and change state to **PreparationFailed**. This is caused by the update resource provider (URP) being unable to properly transfer the files from the storage container to an internal infrastructure share for processing.
- Remediation: Starting with version 1901 (1.1901.0.95), you can work around this issue by clicking **Update now** again (not **Resume**). The URP then cleans up the files from the previous attempt, and restarts the download. If the problem persists, we recommend manually uploading the update package by following the [Import and install updates section](azure-stack-apply-updates.md).
- Occurrence: Common

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>Administrative subscriptions

- Applicable: This issue applies to all supported releases.
- Cause: The two administrative subscriptions that were introduced with version 1804 should not be used. The subscription types are **Metering** subscription, and **Consumption** subscription.
- Remediation: If you have resources running on these two subscriptions, recreate them in user subscriptions.
- Occurrence: Common

### <a name="subscriptions-properties-blade"></a>Subscriptions Properties blade

- Applicable: This issue applies to all supported releases.
- Cause: In the administrator portal, the **Properties** blade for subscriptions does not load correctly
- Remediation: You can view these subscription properties in the **Essentials** pane of the **Subscriptions Overview** blade.
- Occurrence: Common

### <a name="subscription-permissions"></a>Subscription permissions

- Applicable: This issue applies to all supported releases.
- Cause: You cannot view permissions to your subscription using the Azure Stack portals.
- Remediation: Use [PowerShell to verify permissions](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence: Common

### <a name="storage-account-settings"></a>存储帐户设置

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the storage account **Configuration** blade shows an option to change **security transfer type**. The feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="upload-blob"></a>上传 blob

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you try to upload a blob using the **OAuth(preview)** option, the task fails with an error message.
- Remediation: Upload the blob using the SAS option.
- Occurrence: Common

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

- Applicable: This issue applies to all supported releases. 
- Cause: When adding Avaiability Set VMs to the backend pool of a Load Balancer, an error message is being displayed on the portal stating **Failed to save load balancer backend pool**. This is a cosmetic issue on the portal, the functionality is still in place and VMs are successfully added to the backend pool interally. 
- Occurrence: Common

### <a name="network-security-groups"></a>网络安全组

- Applicable: This issue applies to all supported releases. 
- Cause: An explicit **DenyAllOutbound** rule cannot be created in an NSG as this will prevent all internal communication to infrastructure needed for the VM deployment to complete.
- Occurrence: Common

### <a name="service-endpoints"></a>服务终结点

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network** blade shows an option to use **Service Endpoints**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="network-interface"></a>网络接口

#### <a name="addingremoving-network-interface"></a>Adding/Removing Network Interface

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding/removing a network interface.
- Occurrence: Common

#### <a name="primary-network-interface"></a>Primary Network Interface

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding/removing a network interface.
- Occurrence: Common

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network Gateway** blade shows an option to use **Alerts**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="active-active"></a>主动-主动

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, while creating, and in the resource menu of **Virtual Network Gateway**, you will see an option to enable **Active-Active** configuration. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="vpn-troubleshooter"></a>VPN troubleshooter

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Connections** blade shows a feature called **VPN Troubleshooter**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="network-connection-type"></a>Network Connection Type

- Applicable: This issue applies to any 1906 or 1907 environment. 
- Cause: In the user portal, the **AddConnection** blade shows an option to use **VNet-to-VNet**. This feature is currently not supported in Azure Stack. 
- Occurrence: Common 

#### <a name="documentation"></a>文档

- Applicable: This issue applies to all supported releases.
- Cause: The documentation links in the overview page of Virtual Network gateway link to Azure-specific documentation instead of Azure Stack. Use the following links for the Azure Stack documentation:

  - [Gateway SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Highly Available Connections](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configure BGP on Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute circuits](azure-stack-connect-expressroute.md)
  - [Specify custom IPsec/IKE policies](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM boot diagnostics

- Applicable: This issue applies to all supported releases.
- Cause: When creating a new Windows virtual machine (VM), the following error may be displayed: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** . The error occurs if you enable boot diagnostics on a VM, but delete your boot diagnostics storage account.
- Remediation: Recreate the storage account with the same name you used previously.
- Occurrence: Common

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Create failures during patch and update on 4-node Azure Stack environments

- Applicable: This issue applies to all supported releases.
- Cause: Creating VMs in an availability set of 3 fault domains and creating a virtual machine scale set instance fails with a **FabricVmPlacementErrorUnsupportedFaultDomainSize** error during the update process on a 4-node Azure Stack environment.
- Remediation: You can create single VMs in an availability set with 2 fault domains successfully. However, scale set instance creation is still not available during the update process on a 4-node Azure Stack.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH access

- Applicable: This issue applies to all supported releases.
- Cause: An Ubuntu 18.04 VM created with SSH authorization enabled does not allow you to use the SSH keys to sign in.
- Remediation: Use VM access for the Linux extension to implement SSH keys after provisioning, or use password-based authentication.
- Occurrence: Common

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Virtual machine scale set reset password does not work

- Applicable: This issue applies to the 1906 and 1907 releases.
- Cause: A new reset password blade appears in the scale set UI, but Azure Stack does not support resetting password on a scale set yet.
- Remediation: None.
- Occurrence: Common

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Rainy cloud on scale set diagnostics

- Applicable: This issue applies to the 1906 and 1907 releases.
- Cause: The virtual machine scale set overview page shows an empty chart. Clicking on the empty chart opens a "rainy cloud" blade. This is the chart for scale set diagnostic information, such as CPU percentage, and is not a feature supported in the current Azure Stack build.
- Remediation: None.
- Occurrence: Common

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtual machine diagnostic settings blade

- Applicable: This issue applies to the 1906 and 1907 releases.    
- Cause: The virtual machine diagnostic settings blade has a **Sink** tab, which asks for an **Application Insight Account**. This is the result of a new blade and is not yet supported in Azure Stack.
- Remediation: None.
- Occurrence: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>1906 update process

- Applicable: This issue applies to all supported releases.
- Cause: When attempting to install the 1906 Azure Stack update, the status for the update might fail and change state to **PreparationFailed**. This is caused by the update resource provider (URP) being unable to properly transfer the files from the storage container to an internal infrastructure share for processing. 
- Remediation: Starting with version 1901 (1.1901.0.95), you can work around this issue by clicking **Update now** again (not **Resume**). The URP then cleans up the files from the previous attempt, and restarts the download. If the problem persists, we recommend manually uploading the update package by following the [Import and install updates section](azure-stack-apply-updates.md).
- Occurrence: Common

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>Administrative subscriptions

- Applicable: This issue applies to all supported releases.
- Cause: The two administrative subscriptions that were introduced with version 1804 should not be used. The subscription types are **Metering** subscription, and **Consumption** subscription.
- Remediation: If you have resources running on these two subscriptions, recreate them in user subscriptions.
- Occurrence: Common

### <a name="subscription-resources"></a>Subscription resources

- Applicable: This issue applies to all supported releases.
- Cause: Deleting user subscriptions results in orphaned resources.
- Remediation: First delete user resources or the entire resource group, and then delete the user subscriptions.
- Occurrence: Common

### <a name="subscription-permissions"></a>Subscription permissions

- Applicable: This issue applies to all supported releases.
- Cause: You cannot view permissions to your subscription using the Azure Stack portals.
- Remediation: Use [PowerShell to verify permissions](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence: Common

### <a name="subscriptions-properties-blade"></a>Subscriptions Properties blade
- Applicable: This issue applies to all supported releases.
- Cause: In the administrator portal, the **Properties** blade for Subscriptions does not load correctly
- Remediation: You can view these subscriptions properties in the Essentials pane of the Subscriptions Overview blade
- Occurrence: Common

### <a name="storage-account-settings"></a>存储帐户设置

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the storage account **Configuration** blade shows an option to change **security transfer type**. The feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="upload-blob"></a>上传 blob

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you try to upload a blob using the **OAuth(preview)** option, the task fails with an error message.
- Remediation: Upload the blob using the SAS option.
- Occurrence: Common

### <a name="update"></a>更新

- Applicable: This issue applies to the 1906 release.
- Cause: In the operator portal, update status for the hotfix shows an incorrect state for the update. Initial state indicates that the update failed to install, even though it is still in progress.
- Remediation: Refresh the portal and the state will update to "in progress."
- Occurrence: Intermittent

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network** blade shows an option to use **Service Endpoints**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

### <a name="network-interface"></a>网络接口

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding/removing a network interface.
- Occurrence: Common

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network Gateway** blade shows an option to use **Alerts**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="active-active"></a>主动-主动

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, while creating, and in the resource menu of **Virtual Network Gateway**, you will see an option to enable **Active-Active** configuration. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="vpn-troubleshooter"></a>VPN troubleshooter

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Connections** blade shows a feature called **VPN Troubleshooter**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### <a name="documentation"></a>文档

- Applicable: This issue applies to all supported releases.
- Cause: The documentation links in the overview page of Virtual Network gateway link to Azure-specific documentation instead of Azure Stack. Please use the following links for the Azure Stack documentation:

  - [Gateway SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Highly Available Connections](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configure BGP on Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute circuits](azure-stack-connect-expressroute.md)
  - [Specify custom IPsec/IKE policies](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡

#### <a name="add-backend-pool"></a>添加后端池

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, if you attempt to add a **Backend Pool** to a **Load Balancer**, the operation fails with the error message **failed to update Load Balancer...** .
- Remediation: Use PowerShell, CLI or a Resource Manager template to associate the backend pool with a load balancer resource.
- Occurrence: Common

#### <a name="create-inbound-nat"></a>Create inbound NAT

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, if you attempt to create an **Inbound NAT Rule** for a **Load Balancer**, the operation fails with the error message **Failed to update Load Balancer...** .
- Remediation: Use PowerShell, CLI or a Resource Manager template to associate the backend pool with a load balancer resource.
- Occurrence: Common

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM boot diagnostics

- Applicable: This issue applies to all supported releases.
- Cause: When creating a new Windows virtual machine (VM), the following error may be displayed: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** . The error occurs if you enable boot diagnostics on a VM, but delete your boot diagnostics storage account.
- Remediation: Recreate the storage account with the same name you used previously.
- Occurrence: Common

### <a name="virtual-machine-scale-set"></a>虚拟机规模集


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Create failures during patch and update on 4-node Azure Stack environments

- Applicable: This issue applies to all supported releases.
- Cause: Creating VMs in an availability set of 3 fault domains and creating a virtual machine scale set instance fails with a **FabricVmPlacementErrorUnsupportedFaultDomainSize** error during the update process on a 4-node Azure Stack environment.
- Remediation: You can create single VMs in an availability set with 2 fault domains successfully. However, scale set instance creation is still not available during the update process on a 4-node Azure Stack.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH access

- Applicable: This issue applies to all supported releases.
- Cause: An Ubuntu 18.04 VM created with SSH authorization enabled does not allow you to use the SSH keys to sign in.
- Remediation: Use VM access for the Linux extension to implement SSH keys after provisioning, or use password-based authentication.
- Occurrence: Common

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Virtual machine scale set reset password does not work

- Applicable: This issue applies to the 1906 release.
- Cause: A new reset password blade appears in the scale set UI, but Azure Stack does not support resetting password on a scale set yet.
- Remediation: None.
- Occurrence: Common

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Rainy cloud on scale set diagnostics

- Applicable: This issue applies to the 1906 release.
- Cause: The virtual machine scale set overview page shows an empty chart. Clicking on the empty chart opens a "rainy cloud" blade. This is the chart for scale set diagnostic information, such as CPU percentage, and is not a feature supported in the current Azure Stack build.
- Remediation: None.
- Occurrence: Common

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtual machine diagnostic settings blade

- Applicable: This issue applies to the 1906 release.
- Cause: The virtual machine diagnostic settings blade has a **Sink** tab, which asks for an **Application Insight Account**. This is the result of a new blade and is not yet supported in Azure Stack.
- Remediation: None.
- Occurrence: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="archive"></a>存档

To access archived known issues for an older version, use the version selector dropdown above the table of contents on the left, and select the version you want to see.

## <a name="next-steps"></a>后续步骤

- [Review update activity checklist](release-notes-checklist.md)
- [Review list of security updates](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>1905 archived known issues
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>1904 archived known issues
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>1903 archived known issues
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>1902 archived known issues
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>1901 archived known issues
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>1811 archived known issues
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>1809 archived known issues
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>1808 archived known issues
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>1807 archived known issues
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>1805 archived known issues
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>1804 archived known issues
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>1803 archived known issues
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>1802 archived known issues
::: moniker-end

::: moniker range="<azs-1906"
You can access [older versions of Azure Stack known issues on the TechNet Gallery](https://aka.ms/azsarchivedrelnotes). These archived documents are provided for reference purposes only and do not imply support for these versions. For information about Azure Stack support, see [Azure Stack servicing policy](azure-stack-servicing-policy.md). For further assistance, contact Microsoft Customer Support Services.
::: moniker-end
