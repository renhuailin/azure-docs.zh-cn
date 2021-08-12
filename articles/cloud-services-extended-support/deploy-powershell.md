---
title: 部署云服务（外延支持）- PowerShell
description: 使用 PowerShell 部署云服务（外延支持）
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: b61d7adf1b03b1ce44125bc485eb0b2d6221f8f2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460862"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>使用 Azure PowerShell 部署云服务（外延支持）

本文介绍如何使用 `Az.CloudService` PowerShell 模块在具有多个角色（WebRole 和 WorkerRole）和远程桌面扩展的 Azure中部署云服务（外延支持）。 

## <a name="before-you-begin"></a>在开始之前

查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)，并创建关联的资源。 

## <a name="deploy-a-cloud-services-extended-support"></a>部署云服务（外延支持）
1. 安装 Az.CloudService PowerShell 模块  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. 创建新的资源组。 如果使用现有资源组，则可选择性地执行此步骤。   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. 创建一个存储帐户和容器，这将用于存储云服务包文件 (.cspkg) 和服务配置文件 (.cscfg)。 必须对存储帐户名使用唯一的名称。 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. 将云服务包 (.cspkg) 上传到存储帐户。

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  将云服务配置 (cscfg) 上传到存储帐户。 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. 创建虚拟网络和子网。 如果使用现有网络和子网，则可选择性地执行此步骤。 此示例将单个虚拟网络和子网用于两个云服务角色（WebRole 和 WorkerRole）。 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. 创建公共 IP 地址，并设置公共 IP 地址的 DNS 标签属性。 云服务（外延支持）仅支持[基本](../virtual-network/public-ip-addresses.md#basic) SKU 公共 IP 地址。 标准 SKU 公共 IP 不适用于云服务。
如果使用的是静态 IP，则需要在服务配置 (.cscfg) 文件中将其作为保留 IP 引用。 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. 创建网络配置文件对象，并将公共 IP 地址与负载均衡器的前端相关联。 Azure 平台会自动在云服务资源所在的订阅中创建“经典”SKU 负载均衡器资源。 负载均衡器资源是 ARM 中的只读资源。 仅通过云服务部署文件（.cscfg 和 .csdef）支持对该资源的任何更新

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. 创建 Key Vault。 此 Key Vault 将用于存储与云服务（外延支持）角色关联的证书。 Key Vault 必须与云服务位于同一区域和订阅中，并且名称必须唯一。 有关详细信息，请参阅[在 Azure 云服务（外延支持）中使用证书](certificates-and-key-vault.md)。

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. 更新 Key Vault 访问策略，并向用户帐户授予证书权限。 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    或者，通过 ObjectId 设置访问策略（可通过运行 `Get-AzADUser` 获得） 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. 在本示例中，我们会将自签名证书添加到 Key Vault。 需要在云服务配置文件 (.cscfg) 中添加证书指纹才能在云服务角色上进行部署。 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. 创建 OS 配置文件内存中对象。 OS 配置文件指定与云服务角色关联的证书。 该证书与在上一步中创建的证书相同。 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. 创建角色配置文件内存中对象。 角色配置文件定义角色 SKU 的特定属性，例如名称、容量和层级。 在此示例中，我们定义了两个角色：frontendRole 和 backendRole。 角色配置文件信息应与配置文件 (cscfg) 和服务定义文件 (csdef) 中定义的角色配置相匹配。 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. （可选）创建要添加到云服务的扩展配置文件内存中对象。 在此示例中，我们将添加 RDP 扩展。 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    请注意，configFile 应该只具有 PublicConfig 标记，并且应该包含命名空间，如下所示：
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. （可选）将标记定义为要添加到云服务的 PowerShell 哈希表。 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. 使用配置文件对象和 SAS URL 创建云服务部署。

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>后续步骤 
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)