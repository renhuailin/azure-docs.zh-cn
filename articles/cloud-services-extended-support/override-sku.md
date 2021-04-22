---
title: 在 Azure 云服务（外延支持）中针对 CSCFG/CSDEF 替代 SKU 信息
description: 在 Azure 云服务（外延支持）中针对 CSCFG/CSDEF 替代 SKU 信息
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739253"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>在云服务（外延支持）中针对 CSCFG/CSDEF 替代 SKU 信息 

此功能可让用户使用 allowModelOverride 属性更新其云服务中的角色大小和实例计数，而无需更新服务配置和服务定义文件，从而允许云服务在无需重新打包和重新部署的情况下纵向扩展/纵向缩减/横向缩减/横向扩展。

## <a name="set-allowmodeloverride-property"></a>设置 allowModelOverride 属性
可按以下方式设置 allowModelOverride 属性：
* 如果 allowModelOverride 为 true，API 调用将更新云服务的角色大小和实例计数，而不会通过 csdef 和 cscfg 文件验证这些值。 
> [!Note]
> cscfg 将被更新以反映角色实例计数，但 csdef（在 cspkg 内）将保留旧值
* 如果 allowModelOverride 为 false，则当角色大小和实例计数值分别与 csdef 和 cscfg 文件不匹配时，API 调用将引发错误

默认值设置为 false。 如果将属性从 true 重置为 false，则会再次检查 csdef 和 cscfg 文件进行验证。

请通过以下示例，在 PowerShell、模板和 SDK 中应用该属性

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板
如果在此处将属性“allowModelOverride”设置为 true，将用 roleProfile 部分中定义的角色属性更新云服务
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
如果对新的 New-AzCloudService cmdlet 设置了开关“AllowModelOverride”，将用 RoleProfile 中定义的 SKU 属性更新云服务
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
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
如果将变量 AllowModelOverride 设置为 true，将用 RoleProfile 中定义的 SKU 属性更新云服务

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
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure 门户
门户不允许以上属性替代 csdef 和 cscfg 中的角色大小和实例计数。 


## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.md)。
