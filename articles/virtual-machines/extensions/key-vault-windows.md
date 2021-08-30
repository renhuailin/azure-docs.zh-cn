---
title: 适用于 Windows 的 Azure Key Vault VM 扩展
description: 部署一个代理，该代理使用虚拟机扩展在虚拟机上执行 Key Vault 密钥自动刷新操作。
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d7424b6ad88bc7e77a4b7d191feb54658f67ff21
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220670"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>适用于 Windows 的 Key Vault 虚拟机扩展

密钥保管库 VM 扩展可自动刷新 Azure 密钥保管库中存储的证书。 具体而言，该扩展监视一系列观测到存储在 Key Vault 中的证书，并在检测到更改时检索并安装相应的证书。 本文档详细介绍适用于 Windows 的 Key Vault VM 虚拟机扩展支持的平台、配置和部署选项。 

### <a name="operating-system"></a>操作系统

密钥保管库 VM 扩展支持以下版本的 Windows：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

自定义本地 VM 也支持 Key Vault VM 扩展，该 VM 已上传并转换为专用映像，以通过 Windows Server 2019 核心安装用在 Azure 中。

### <a name="supported-certificate-content-types"></a>支持的证书内容类型

- PKCS #12
- PEM

## <a name="prerequisites"></a>先决条件

  - 具有证书的 Key Vault 实例。 请参阅[创建 Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM 必须已分配[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
  - 必须使用机密 `get` 和 `list` 权限为 VM/VMSS 托管标识设置 Key Vault 访问策略，以检索证书的机密部分。 请参阅[如何向 Key Vault 进行身份验证](../../key-vault/general/authentication.md)和[分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-cli.md)。
  -  虚拟机规模集应具有以下标识设置：

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - AKV 扩展应具有以下设置：

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Key Vault VM 代理扩展的架构。 该扩展不需要受保护的设置 - 其所有设置都被视为公共信息。 该扩展需要受监视的证书列表、轮询频率和目标证书存储。 具体而言：  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine 或 CurrentUser（区分大小写） | string |
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
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>扩展依赖项排序
Key Vault VM 扩展支持扩展排序（如果已配置）。 默认情况下，扩展在开始轮询后会立即报告它已成功启动。 但是，可以将其配置为等到成功下载证书的完整列表之后再报告成功启动。 如果其他扩展依赖于在启动之前安装全套证书，则启用此设置将使那些扩展可以声明 Key Vault 扩展的依赖项。 这将阻止启动那些扩展，直到安装了其所依赖的所有证书为止。 扩展将一直重试初始下载，并保持 `Transitioning` 状态。

若要启用此功能，请设置以下项：
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> 使用此功能与 ARM 模板不兼容（该模板会创建系统分配的标识并使用该标识更新 Key Vault 访问策略）。 这样做将导致死锁，因为在所有扩展启动之前，无法更新保管库访问策略。 应改为在部署之前使用单个用户分配的 MSI 标识，并使用该标识对你的保管库进行预 ACL 操作。

## <a name="azure-powershell-deployment"></a>Azure PowerShell 部署

> [!WARNING]
> PowerShell 客户端通常会将 `\` 添加到 settings.json 中的 `"`，这会导致 akvvm_service 失败，并显示错误 `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`。在门户中“设置”下的“扩展”中将显示额外的 `\` 和 `"` 字符。 为了避免这种情况，请将 `$settings` 初始化为 PowerShell `HashTable`：
> 
> ```powershell
> $settings = @{
>     "secretsManagementSettings" = @{ 
>         "pollingIntervalInS"       = "<pollingInterval>"; 
>         "certificateStoreName"     = "<certStoreName>"; 
>         "certificateStoreLocation" = "<certStoreLoc>"; 
>         "observedCertificates"     = @("<observedCert1>", "<observedCert2>") } }
> ```
>
  
可以使用 Azure PowerShell，将 Key Vault VM 扩展部署到现有虚拟机或虚拟机规模集。 

* 在 VM 上部署该扩展：
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
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
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
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
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* 在虚拟机规模集上部署该扩展：

   ```azurecli
        # Start the deployment
        az vmss extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

请注意以下限制/要求：
- Key Vault 限制：
  - 必须在部署时存在 
  - 必须使用托管标识为 VM/VMSS 标识设置 Key Vault 访问策略。 请参阅[如何向 Key Vault 进行身份验证](../../key-vault/general/authentication.md)和[分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-cli.md)。

## <a name="troubleshoot-and-support"></a>故障排除和支持

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
Key Vault VM 扩展日志仅存在于本地 VM 上，并且在进行故障排除时最能提供信息

|位置|说明|
|--|--|
| C:\WindowsAzure\Logs\WaAppAgent.log | 显示进行扩展更新的时间。 |
| C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows<most recent version>\ | 显示证书下载的状态。 下载位置始终为 Windows 计算机的 MY 存储位置 (certlm.msc)。 |
| C:\Packages\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows<most recent version>\RuntimeSettings\ |   Key Vault VM 扩展服务日志显示 akvvm_service 服务的状态。 |
| C:\Packages\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows<most recent version>\Status\  | Key Vault VM 扩展服务的配置和二进制文件。 |
|||  


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
