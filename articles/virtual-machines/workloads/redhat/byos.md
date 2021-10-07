---
title: Red Hat Enterprise Linux 自带订阅 Azure 映像 | Microsoft Docs
description: 了解 Azure 上的 Red Hat Enterprise Linux 自带订阅映像。
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 06/10/2020
ms.author: mamccrea
ms.openlocfilehash: 59a89c069c332d4d0346e0f3c795763818c601de
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457100"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Azure 中的 Red Hat Enterprise Linux 自带订阅黄金映像

**适用于：** :heavy_check_mark: Linux VM 

Red Hat Enterprise Linux (RHEL) 映像在 Azure 中通过即用即付或自带订阅 (BYOS)（Red Hat 黄金映像）模型提供。 本文概述了 Azure 中的 Red Hat 黄金映像。

>[!NOTE]
> RHEL BYOS 黄金映像在 Azure 公用（商业）和 Azure 政府云中提供。 它们在 Azure 中国或 Azure Blackforest 云中不可用。

## <a name="important-points-to-consider"></a>考虑的要点

- 此计划中提供的 Red Hat 黄金映像是与 Azure 市场中的 RHEL 即用即付映像类似的生产就绪 RHEL 映像。
- 这些映像遵循在 [Azure 上的 Red Hat Enterprise Linux 映像](./redhat-images.md)中所述的当前策略。
- 标准版支持策略适用于通过这些映像创建的 VM。
- 从 Red Hat 黄金映像预配的 VM 不会产生与 RHEL 即用即付映像关联的 RHEL 费用。
- 映像未获授权。 必须使用 Red Hat 订阅管理器注册和订阅 VM，才能直接从 Red Hat 获取更新。
- 可以使用 [Azure 混合权益](../../linux/azure-hybrid-benefit-linux.md)从即用即付映像切换到 BYOS。 但是对于 Linux 映像，无法从最初部署的 BYOS 切换到即用即付计费模型。 若要将计费模型从 BYOS 切换为即用即付，必须从相应的映像重新部署 VM。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>访问 Red Hat 黄金映像的要求和条件

1. 熟悉 [Red Hat Cloud Access 计划](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)条款。 在 [Red Hat 订阅管理器](https://access.redhat.com/management/cloud)处为 Cloud Access 启用 Red Hat 订阅。 需要准备好将为 Cloud Access 注册的 Azure 订阅。

1. 如果为 Cloud Access 启用的 Red Hat 订阅满足资格要求，则会自动为金牌映像访问启用 Azure 订阅。

### <a name="expected-time-for-image-access"></a>映像访问的预计时间

完成 Cloud Access 启用步骤后，Red Hat 会验证你是否符合 Red Hat 黄金映像资格。 如果验证成功，则你会在三小时内收到黄金映像的访问权限。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>通过 Azure 门户使用 Red Hat 黄金映像

1. Azure 订阅收到 Red Hat 黄金映像的访问权限后，你可以在 [Azure 门户](https://portal.azure.com)中找到它们。 转到“创建资源” > “查看全部” 。

1. 在页面顶部，你会看到你拥有专用套餐。

    ![市场专用套餐](./media/rhel-byos-privateoffers.png)

1. 选择紫色链接，或向下滚动到页面底部，以查看你的专用套餐。

1. UI 中的其余预配与任何其他现有 Red Hat 映像没有什么不同。 选择 RHEL 版本，并按照提示预配 VM。 此过程还允许在最后一步接受映像条款。

>[!NOTE]
>到目前为止，这些步骤不会启用 Red Hat 黄金映像来进行编程部署。 这需要其他步骤，如“其他信息”部分中所述。

本文档的其余部分重点介绍用于预配映像和接受其条款的 CLI 方法。 就最终结果（已预配的 RHEL 黄金映像 VM）而言，UI 和 CLI 完全可以互换。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>通过 Azure CLI 使用 Red Hat 黄金映像

以下说明会引导你完成使用 Azure CLI 进行 RHEL VM 初始部署的过程。 这些说明假设已[安装 Azure CLI](/cli/azure/install-azure-cli)。

>[!IMPORTANT]
>请确保对于以下所有命令，在发布者、讨教、计划和映像引用中全部使用小写字母。

1. 检查你是否在使用所需订阅。

    ```azurecli
    az account show -o=json
    ```

1. 为 Red Hat 黄金映像 VM 创建资源组。

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受映像条款。

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >对于每个 Azure 订阅、每个映像 SKU，需要接受一次这些条款。

1. （可选）使用以下命令验证 VM 部署：

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. 通过运行上一个示例中所示的相同命令来预配 VM，无需 `--validate` 参数。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. 通过 SSH 连接到 VM，并验证是否具有未获授权的映像。 若要执行此步骤，请运行 `sudo yum repolist`。 对于 RHEL 8，请使用 `sudo dnf repolist`。 输出会要求使用订阅管理器向 Red Hat 注册 VM。

>[!NOTE]
>在 RHEL 8 上，`dnf` 和 `yum` 可互换。 有关详细信息，请参阅 [RHEL 8 管理员指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)。

## <a name="use-the-red-hat-gold-images-from-powershell"></a>通过 PowerShell 使用 Red Hat 黄金映像

下面的脚本就是一个示例。 将资源组、位置、VM 名称、登录信息和其他变量替换为你选择的配置。 发布者和计划信息必须为小写。

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>对 Red Hat Enterprise Linux 自带订阅黄金映像进行加密

可以使用 [Azure 磁盘加密](../../linux/disk-encryption-overview.md)保护 Red Hat Enterprise Linux BYOS 黄金映像。 必须先注册订阅，然后才能启用加密。 有关如何注册 RHEL BYOS 黄金映像的详细信息，请参阅[如何使用 Red Hat 订阅管理器将系统注册并订阅到 Red Hat 客户门户](https://access.redhat.com/solutions/253273)。 如果有活动 Red Hat 订阅，还可以阅读[创建 Red Hat 客户门户激活密钥](https://access.redhat.com/articles/1378093)。

[Red Hat 自定义映像](../../linux/redhat-create-upload-vhd.md)不支持 Azure 磁盘加密。 [适用于 Linux VM 的 Azure 磁盘加密](../../linux/disk-encryption-overview.md#additional-vm-requirements)中介绍了其他 Azure 磁盘加密要求和先决条件。

有关应用 Azure 磁盘加密的步骤，请参阅 [Linux VM 上的 Azure 磁盘加密方案](../../linux/disk-encryption-linux.md)和相关文章。

## <a name="additional-information"></a>其他信息

- 如果尝试在未针对此套餐启用的订阅上预配 VM，则会收到以下消息：

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    在这种情况下，请与 Microsoft 或 Red Hat 联系以启用订阅。

- 如果从 RHEL BYOS 映像修改快照并尝试将该自定义映像发布到[共享映像库](../../shared-image-galleries.md)，则必须提供与快照原始源相匹配的计划信息。 例如，命令可能如下所示：

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    请注意最后一行中的计划参数。

    自定义映像不支持 [Azure 磁盘加密](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)。

- 如果使用自动化从 RHEL BYOS 映像预配 VM，则必须提供与示例命令中所示内容相似的计划参数。 例如，如果使用 Terraform，则在[计划块](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供计划信息。

## <a name="next-steps"></a>后续步骤

- [Red hat 公有云文档](https://access.redhat.com/public-cloud)中提供了有关 Red Hat Cloud Access 的更多详细信息
- 有关 Cloud Access 的分步指南和计划详细信息，请参阅 [Red Hat Cloud Access 文档](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)。
- 若要详细了解 Red Hat 更新基础结构，请参阅 [Azure Red Hat 更新基础结构](./redhat-rhui.md)。
- 若要详细了解 Azure 中的所有 Red Hat 映像，请参阅[文档页面](./redhat-images.md)。
- 有关 RHEL 所有版本的 Red Hat 支持策略的信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)页。
- 有关 RHEL 黄金映像的其他文档，请参阅 [Red Hat 文档](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access#proc_using-gold-images-azure_cloud-access)。
