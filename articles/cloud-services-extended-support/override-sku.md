---
title: 在 Azure 云服务（外延支持）中针对 CSCFG/CSDEF 替代 SKU 信息
description: 本文介绍如何在 Azure 云服务（外延支持）的 .cscfg 和 .csdef 文件中替代 SKU 信息。
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31ff47cd4e110e62769678836bdd803b71369e0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437872"
---
# <a name="override-sku-settings-in-cscfg-and-csdef-files-for-cloud-services-extended-support"></a>替代云服务（外延支持） .cscfg 和 .csdef 文件中的 SKU 设置

本文介绍如何使用 allowModelOverride 属性更新 Azure 云服务中的角色大小和实例计数。 使用此属性时，无需更新服务配置 (.cscfg) 和服务定义 (.csdef) 文件。 因此，可在不重新打包以及不重新部署的情况下，将云服务向上、向下调整规模，以及缩减或扩展。

## <a name="set-the-allowmodeloverride-property"></a>设置 allowModelOverride 属性
可将 allowModelOverride 属性设置为 `true` 或 `false`。 
* 若 allowModelOverride 设置为 `true`，API 调用将更新云服务的角色大小和实例计数，而不会通过 .csdef 和 .cscfg 文件验证这些值。 
   > [!Note]
   > .cscfg 文件将会更新，以反映角色实例计数。 .csdef 文件（嵌入于 .cspkg 中）将保留旧值。

* 若 allowModelOverride 设置为 `false`，则当角色大小和实例计数值分别与 .csdef 和 .cscfg 文件中的值不匹配时，API 调用将引发错误。

默认值是 `false`。 若属性设置为 `true` 后又重置为 `false`，则系统将再次验证 .csdef 文件和 .cscfg 文件。

以下示例演示如何使用 Azure 资源管理器 (ARM) 模板、PowerShell 或 SDK 来设置 allowModelOverride 属性。

### <a name="arm-template"></a>ARM 模板
若在此处将 allowModelOverride 属性设置为 `true`，则系统将用 `roleProfile` 部分中定义的角色属性更新云服务：
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
若对新的 `New-AzCloudService` cmdlet 设置了 `AllowModelOverride` 开关，则系统将用角色配置文件中定义的 SKU 属性更新云服务：
```powershell
New-AzCloudService ` 
-Name "ContosoCS" ` 
-ResourceGroupName "ContosOrg" ` 
-Location "East US" `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK 中 IsInRole 中的声明
若将 `AllowModelOverride` 变量设置为 `true`，则系统将用角色配置文件中定义的 SKU 属性更新云服务：

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate("ContosOrg", "ContosoCS", cloudService);
```
### <a name="azure-portal"></a>Azure 门户
Azure 门户不允许使用 allowModelOverride 属性来替代 .csdef 和 .cscfg 文件中的角色大小和实例计数。 


## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅关于云服务（外延支持）的[常见问题解答](faq.yml)。
