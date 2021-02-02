---
title: Azure PowerShell 示例 - 创建 Azure 云服务（外延支持）
description: 用于创建 Azure 云服务（外延支持）部署的示例脚本
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 61c8924b5dba37e3fc61da4710b9bcdad34ebeca
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881528"
---
# <a name="create-a-new-azure-cloud-service-extended-support"></a>创建新的 Azure 云服务（外延支持）
这些示例涵盖了新建 Azure 云服务（外延支持）部署的各种方式。

## <a name="create-new-cloud-service-with-single-role"></a>使用单个角色创建新的云服务

```powershell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                       `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig
```


## <a name="create-new-cloud-service-with-single-role-and-remote-desktop-extension"></a>使用单个角色和远程桌面扩展创建新的云服务

```powershell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosoOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Create RDP extension object
$credential = Get-Credential
$expiration = (Get-Date).AddYears(1)
$extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1'

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                        `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-ExtensionProfileExtension $extension
```

## <a name="create-a-cloud-service-with-a-single-role-and-windows-azure-diagnostics-extension"></a>使用单个角色和 Windows Azure 诊断扩展创建云服务

```PowerShell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosoOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "ContosSA"
$configFile = "<WAD configuration file path>"
$extension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

## Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                        `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-ExtensionProfileExtension $extension
```
## <a name="create-new-cloud-service-with-single-role-and-certificate-from-key-vault"></a>使用 Key Vault 中的单个角色和证书创建新的云服务

```powershell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create OS profile object
$keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault
$certificate=Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert
$secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
 $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                        `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-OSProfileSecret $secretGroup
```

## <a name="create-new-cloud-service-with-multiple-roles-and-extensions"></a>使用多个角色和扩展创建新的云服务

```powershell
# Create role profile object
 $role1 = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2
 $role2 = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2
 $roles = @($role1, $role2)

# Create network profile object
 $publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp
 $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
 $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Create RDP extension object
 $credential = Get-Credential
 $expiration = (Get-Date).AddYears(1)
 $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1'

# Create Geneva extension object
 $genevaExtension = New-AzCloudServiceExtensionObject -Name GenevaExtension -Publisher Microsoft.Azure.Geneva -Type GenevaMonitoringPaaS -TypeHandlerVersion "2.14.0.2"
 $extensions = @($rdpExtension, $genevaExtension)

# Add tags
$tag=@{"Owner" = "Contoso"}

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $roles                                       `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-ExtensionProfileExtension $extensions                        `
-Tag $tag
```

## <a name="next-steps"></a>后续步骤

- 有关 Azure 云服务（外延支持）的详细信息，请参阅 [Azure 云服务（外延支持）概述](overview.md)。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)
