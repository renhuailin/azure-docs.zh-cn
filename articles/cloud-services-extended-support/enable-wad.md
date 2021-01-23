---
title: '在云服务中应用 Windows Azure 诊断扩展 (扩展支持) '
description: '将 Windows Azure 诊断扩展应用于云服务 (扩展支持) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4511ad979312d58e0a1b9cce9b1280e9ca059007
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744250"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>在云服务中应用 Windows Azure 诊断扩展 (扩展支持)  
可以监视任何云服务的关键性能指标。 每个云服务角色收集极少量的数据：CPU 使用率、网络使用率和磁盘利用率。 如果云服务将 Microsoft. 诊断扩展应用于角色，则该角色可以收集更多数据点。 有关详细信息，请参阅 [扩展概述](extensions.md)

可以通过[PowerShell](deploy-powershell.md)或[ARM 模板](deploy-template.md)为云服务启用 (扩展支持) 的 Windows Azure 诊断扩展

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>使用 PowerShell 应用 Windows Azure 诊断扩展

```powershell
# Create WAD extension object
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
$extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>使用 ARM 模板应用 Windows Azure 诊断扩展
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 查看 [云服务的常见问题 (](faq.md) 扩展支持) 。
- ) 使用 [Azure 门户](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [模板](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)部署云服务 (扩展支持。