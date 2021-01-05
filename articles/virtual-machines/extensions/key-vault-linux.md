---
title: 适用于 Linux 的 Azure Key Vault VM 扩展
description: 部署一个代理，该代理使用虚拟机扩展在虚拟机上执行 Key Vault 证书自动刷新操作。
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 0558513d88eb5ffb03484e9d3bd8e37b2c9a0dcf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895013"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>适用于 Linux 的 Key Vault 虚拟机扩展

通过 Key Vault 虚拟机 (VM) 扩展，可自动刷新 Azure Key Vault 上存储的证书。 具体而言，该扩展会监视在密钥保管库中存储的观察到的证书列表。  检测到更改后，该扩展会检索并安装相应的证书。 Key Vault VM 扩展由 Microsoft 发布并提供支持，目前在 Linux VM 上提供。 本文档详细介绍适用于 Linux 的 Key Vault VM 扩展支持的平台、配置和部署选项。 

### <a name="operating-system"></a>操作系统

Key Vault VM 扩展支持以下 Linux 发行版：

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>支持的证书内容类型

- PKCS #12
- PEM

## <a name="prerequisities"></a>先决条件
  - 具有证书的 Key Vault 实例。 请参阅[创建 Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS 必须已分配[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
  - 必须使用机密 `get` 和 `list` 权限为 VM/VMSS 托管标识设置 Key Vault 访问策略，以检索证书的机密部分。 请参阅[如何向 Key Vault 进行身份验证](../../key-vault/general/authentication.md)和[分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-cli.md)。
  -  VMSS 应具有以下标识设置： ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - AKV 扩展应具有此设置： `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Key Vault VM 代理扩展的架构。 该扩展不需要受保护的设置 - 其所有设置都被视为没有安全影响的信息。 该扩展需要受监视的密钥列表、轮询频率和目标证书存储。 具体而言：  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> 观察到的证书 URL 的格式应为 `https://myVaultName.vault.azure.net/secrets/myCertName`。
> 
> 这是因为 `/secrets` 路径将返回包含私钥的完整证书，而 `/certificates` 路径不会。 有关证书的详细信息可在此处找到：[密钥保管库证书](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> 仅对于使用“用户分配的标识”的 VM，才需要“authenticationSettings”属性 。
> 它指定用于对 Key Vault 进行身份验证的标识。


### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 字符串 |
| type | KeyVaultForLinux | 字符串 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 字符串 |
| certificateStoreName | 它在 Linux 上被忽略 | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | string |
| requireInitialSync | 是 | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | 字符串数组
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 部署需要部署后刷新证书的一个或多个虚拟机时，模板是理想选择。 可将该扩展部署到单个 VM 或虚拟机规模集。 架构和配置对于这两种模板类型通用。 

虚拟机扩展的 JSON 配置必须嵌套在模板的虚拟机资源片段中，具体来说是嵌套在虚拟机模板的 `"resources": []` 对象中，对于虚拟机规模集而言，是嵌套在 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 对象下。

 > [!NOTE]
> VM 扩展需要分配有系统或用户托管标识，才能向 Key Vault 进行身份验证。  请参阅[如何向 Key Vault 进行身份验证和分配 Key Vault 访问策略。](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>扩展依赖项排序
如果已配置，Key Vault VM 扩展支持扩展排序。 默认情况下，扩展会在它开始轮询后立即报告已成功启动。 但是，可以将其配置为等待，直到成功下载证书的完整列表，然后再报告成功的启动。 如果其他扩展依赖于在开始之前安装整套证书，则启用此设置将允许这些扩展声明对 Key Vault 扩展的依赖项。 这会阻止这些扩展开始，直到安装了它们所依赖的所有证书。 扩展会无限期地重试初始下载并保持 `Transitioning` 状态。

若要启用此设置，请执行以下操作：
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> 纪录使用此功能与创建系统分配的标识并使用该标识更新 Key Vault 访问策略的 ARM 模板不兼容。 这样做会导致死锁，因为在所有扩展开始之前都无法更新保管库访问策略。 应改为使用 *单个用户分配的 MSI 标识* ，并在部署之前使用该标识预 ACL 保管库。

## <a name="azure-powershell-deployment"></a>Azure PowerShell 部署
> [!WARNING]
> PowerShell 客户端通常会将 `\` 添加到 settings.json 中的 `"`，这会导致 akvvm_service 失败，并出现错误：`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

可以使用 Azure PowerShell，将 Key Vault VM 扩展部署到现有虚拟机或虚拟机规模集。 

* 在 VM 上部署该扩展：
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 在虚拟机规模集上部署该扩展：

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI，将密钥保管库 VM 扩展部署到现有虚拟机或虚拟机规模集。 
 
* 在 VM 上部署该扩展：
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* 在虚拟机规模集上部署该扩展：

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
请注意以下限制/要求：
- Key Vault 限制：
  - 必须在部署时存在 
  - 必须使用托管标识为 VM/VMSS 标识设置 Key Vault 访问策略。 请参阅[如何向 Key Vault 进行身份验证](../../key-vault/general/authentication.md)和[分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-cli.md)。

### <a name="frequently-asked-questions"></a>常见问题

* 可设置的 observedCertificates 数是否有限制？
  没有，Key Vault VM 扩展对 observedCertificates 数没有限制。


### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure PowerShell 运行以下命令。

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>日志和配置

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>使用符号符号

符号链接或符号链接基本上是高级快捷方式。 若要避免监视文件夹和自动获取最新证书，可以使用此符号链接 `([VaultName].[CertificateName])` 在 Linux 上获取最新版本的证书。

### <a name="frequently-asked-questions"></a>常见问题

* 可设置的 observedCertificates 数是否有限制？
  没有，Key Vault VM 扩展对 observedCertificates 数没有限制。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
