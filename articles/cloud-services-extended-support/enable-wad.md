---
title: 在云服务（外延支持）中应用 Windows Azure 诊断扩展
description: 为云服务（外延支持）应用 Windows Azure 诊断扩展
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c6df0ee88d712d36d3b5f57009a844e45232960c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444943"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>在云服务（外延支持）中应用 Windows Azure 诊断扩展 
可以监视任何云服务的关键性能指标。 每个云服务角色收集极少量的数据：CPU 使用率、网络使用率和磁盘利用率。 如果云服务已将 Microsoft.Azure.Diagnostics 扩展应用到某个角色，则该角色可以收集其他数据点。 有关详细信息，请参阅[扩展概述](extensions.md)

可以通过 [PowerShell](deploy-powershell.md) 或 [ARM 模板](deploy-template.md)为云服务（外延支持）启用 Windows Azure 诊断扩展

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>使用 PowerShell 应用 Windows Azure 诊断扩展

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFilePath = "<Insert WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFilePath -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Add <privateConfig> settings
$wadExtension.ProtectedSetting = "<Insert WAD Private Configuration as raw string here>"

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```
通过执行以下 PowerShell 命令下载公共配置文件架构定义：

```powershell
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'PublicWadConfig.xsd'
```
下面是公共配置 XML 文件的示例
```
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```
通过执行以下 PowerShell 命令来下载专用配置文件架构定义：

```powershell
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PrivateConfigurationSchema | Out-File -Encoding utf8 -FilePath 'PrivateWadConfig.xsd'
```
下面是专用 XML 配置文件的示例

```
<?xml version="1.0" encoding="utf-8"?>
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="string" key="string" />
  <AzureMonitorAccount>
    <ServicePrincipalMeta>
      <PrincipalId>string</PrincipalId>
      <Secret>string</Secret>
    </ServicePrincipalMeta>
  </AzureMonitorAccount>
  <SecondaryStorageAccounts>
    <StorageAccount name="string" />
  </SecondaryStorageAccounts>
  <SecondaryEventHubs>
    <EventHub Url="string" SharedAccessKeyName="string" SharedAccessKey="string" />
  </SecondaryEventHubs>
</PrivateConfig>
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
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
