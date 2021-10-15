---
title: 如何为 Azure Spring Cloud 配置 Palo Alto
description: 如何为 Azure Spring Cloud 配置 Palo Alto
author: karlerickson
ms.author: vaangadi
ms.topic: how-to
ms.service: spring-cloud
ms.date: 09/17/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a65a6ea1fb2070b6a1879521b257c2738930bf3a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367509"
---
# <a name="how-to-configure-palo-alto-for-azure-spring-cloud"></a>如何为 Azure Spring Cloud 配置 Palo Alto

本文适用于：✔️ Java ✔️ C#

本文介绍如何将 Azure Spring Cloud 与 Palo Alto 防火墙一起使用。

例如，[Azure Spring Cloud](/azure/spring-cloud/reference-architecture) 引用体系结构包含 Azure 防火墙，以保护应用程序。 但是，如果当前部署包含 Palo Alto 防火墙，则可以从 Azure Spring Cloud 部署中省略 Azure 防火墙，并改为使用 Palo Alto，如本文中所述。

应将配置信息（如规则和地址通配符）保存在 Git 存储库的 CSV 文件中。 本文介绍如何使用自动化将这些文件应用于 Palo Alto。 要了解要应用于 Palo Alto 的配置，请参阅[在 VNET 中运行 Azure Spring Cloud 的客户责任](/azure/spring-cloud/vnet-customer-responsibilities)。 

> [!Note]
> 在描述 REST API 的使用时，本文使用 PowerShell 变量语法来指示由你自行决定的名称和值。 请确保在所有步骤中使用相同的值。
>
> 在 Palo Alto 中配置 TLS/SSL 证书后，请根据以下示例中的所有 Palo Alto REST API 调用删除 `-SkipCertificateCheck` 参数。
>
> 不应将本文用作 Palo Alto REST API 的引用。 所有示例仅用于演示目的。 有关权威的 API 详细信息，请参阅 Palo Alto 文档中的 [PAN-OS REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/pan-os-rest-api.html)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果你没有订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Palo Alto 部署。 如果没有部署，则可以预配[来自 Azure 市场的 Palo Alto](https://ms.portal.azure.com/#create/paloaltonetworks.vmseries-ngfwbundle2)。
* [PowerShell](/powershell/scripting/install/installing-powershell)
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="configure-palo-alto"></a>配置 Palo Alto

首先，配置 Palo Alto VM 系列防火墙。 有关详细说明，请参阅[部署来自 Azure 市场的 VM 系列防火墙（解决方案模板）](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewall-on-azure-solution-template.html)。 这些说明将帮助你预配 VM 系列防火墙，并同时配置 `Trust` 和 `UnTrust` 子网以及关联的网络接口卡。 要保持一致，应在引用体系结构中 `Hub` 虚拟网络的地址空间中创建此防火墙。

[Azure 引用体系结构指南](https://www.paloaltonetworks.com/resources/guides/azure-architecture-guide)探讨了适用于在 Azure 上部署防火墙的几个技术设计模型。

本文的其余部分假定你具有以下两个预配置的网络区域：

* `Trust`，包含连接到与 Azure Spring Cloud 虚拟网络对等互连的虚拟网络的接口。
* `UnTrust`，包含之前在 VM 系列部署指南中创建的公用 Internet 的接口。

## <a name="prepare-csv-files"></a>准备 CSV 文件

接下来，创建三个 CSV 文件。

将第一个文件命名为 AzureSpringCloudServices.csv。 此文件应包含 Azure Spring Cloud 的入口端口。 以下示例中的值仅用于演示目的。 有关所需的所有值，请参阅[在 VNET 中运行 Azure Spring Cloud 的客户责任](/azure/spring-cloud/vnet-customer-responsibilities)的 [Azure Spring Cloud 网络要求](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-network-requirements)部分。

```CSV
name,protocol,port,tag
ASC_1194,udp,1194,AzureSpringCloud
ASC_443,tcp,443,AzureSpringCloud
ASC_9000,tcp,9000,AzureSpringCloud
ASC_445,tcp,445,AzureSpringCloud
ASC_123,udp,123,AzureSpringCloud
```

将第二个文件命名为 AzureSpringCloudUrlCategories.csv。 此文件应包含应可用于来自 Azure Spring Cloud 的出口的地址（有通配符）。 以下示例中的值仅用于演示目的。 有关最新值，请参阅 [Azure Spring Cloud FQDN 要求/应用程序规则](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-fqdn-requirementsapplication-rules)。

```CSV
name,description
*.azmk8s.io,
mcr.microsoft.com,
*.cdn.mscr.io,
*.data.mcr.microsoft.com,
management.azure.com,
*.microsoftonline.com,
*.microsoft.com,
packages.microsoft.com,
acs-mirror.azureedge.net,
mscrl.microsoft.com,
crl.microsoft.com,
crl3.digicert.com
```

将第三个文件命名为 AzureMonitorAddresses.csv。 如果使用的是 Azure Monitor，此文件应包含所有可用于指标和通过 Azure Monitor 进行监视的地址和 IP 范围。 以下示例中的值仅用于演示目的。 有关最新值，请参阅 [Azure Monitor 使用的 IP 地址](/azure/azure-monitor/app/ip-addresses)。

```CSV
name,type,address,tag
40.114.241.141,ip-netmask,40.114.241.141/32,AzureMonitor
104.45.136.42,ip-netmask,104.45.136.42/32,AzureMonitor
40.84.189.107,ip-netmask,40.84.189.107/32,AzureMonitor
168.63.242.221,ip-netmask,168.63.242.221/32,AzureMonitor
52.167.221.184,ip-netmask,52.167.221.184/32,AzureMonitor
live.applicationinsights.azure.com,fqdn,live.applicationinsights.azure.com,AzureMonitor
rt.applicationinsights.microsoft.com,fqdn,rt.applicationinsights.microsoft.com,AzureMonitor
rt.services.visualstudio.com,fqdn,rt.services.visualstudio.com,AzureMonitor

```

## <a name="authenticate-into-palo-alto"></a>在 Palo Alto 中进行身份验证

接下来，需要在 Palo Alto 中进行身份验证并获取 API 密钥。 有关详细信息，请参阅 Palo Alto 文档中的[获取 API 密钥](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-xml-api/get-your-api-key.html)。

以下示例使用 PowerShell 进行身份验证并生成将在本文稍后使用的请求头：

```powershell
$username=<username for PaloAlto>
$password=<password for PaloAlto>
$authResponse = irm "https://${PaloAltoIpAddress}/api/?type=keygen&user=${username}&password=${password}" -SkipCertificateCheck
$paloAltoHeaders = @{'X-PAN-KEY' = $authResponse.response.result.key; 'Content-Type' = 'application/json' }
```

## <a name="delete-existing-service-group"></a>删除现有服务组

如果之前尝试过配置，应重置这些配置并删除任何安全规则和服务组。

使用[安全规则 REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/create-security-policy-rule-rest-api.html) 删除安全规则，如下例所示：

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=${paloAltoSecurityPolicyName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

删除服务组，如下例所示：

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${paloAltoServiceGroupName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

删除（在 AzureSpringCloudServices.csv 中定义的）各个 Palo Alto 服务，如下例所示：

```powershell
Get-Content .\AzureSpringCloudServices.csv | ConvertFrom-Csv | select name | ForEach-Object {
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${_}"
    Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
}
```

## <a name="create-a-service-and-service-group"></a>创建服务和服务组

要根据之前创建的 AzureSpringCloudServices.csv 文件自动创建服务，请使用以下示例。

```powershell
# Define a function to create and submit a Palo Alto service creation request
function New-PaloAltoService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject]
        $ServiceObject
    )
    PROCESS {
        $requestBody = @{
            'entry' = @{
                '@name'    = $ServiceObject.name
                'protocol' = @{
                    $ServiceObject.protocol = @{
                        'port'     = $ServiceObject.port
                        'override' = @{
                            'no' = @{}
                        }

                    }
                }
                'tag'      = @{
                    'member' = @($ServiceObject.tag)
                }
            }
        }

        # Some rules in the CSV may need to conain source ports or descriptions. If these are present, populate them in the request
        if ($ServiceObject.description) {
            $requestBody.entry.description = $ServiceObject.description
        }
        if ($ServiceObject.'source-port') {
            $requestBody.entry.protocol."$($ServiceObject.protocol)".'source-port' = $ServiceObject.'source-port'
        }

        # Send the request
        $name = $requestBody.entry.'@name'
        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${name}"
         Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 9) -Verbose
    }
}

# Now invoke that function for every row in AzureSpringCloudServices.csv
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoService
```

接下来，为这些服务创建服务组，如下例所示：

```powershell
# Create a function to consume service definitions and submit a service group creation request
function New-PaloAltoServiceGroup {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject[]]
        $RuleData,

        [Parameter(Mandatory = $true)]
        [string]
        $ServiceGroupName
    )
    begin {
        [array] $names = @()
    }

    process {
        $names += $RuleData.name
    }

    end {
        $requestBody = @{ 'entry' = [ordered] @{
                '@name'   = $ServiceGroupName
                'members' = @{ 'member' = $names }
                'tag'     = @{ 'member' = 'AzureSpringCloud' }
            }
        }

        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${ServiceGroupName}"

        Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json $requestBody) -Verbose
    }
}

# Run that function for all services in AzureSpringCloudServices.csv.
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoServiceGroup -ServiceGroupName 'AzureSpringCloud_SG'
```

## <a name="create-custom-url-categories"></a>创建自定义 URL 类别

接下来，为服务组定义自定义 URL 类别，以启用来自 Azure Spring Cloud 的出口，如下例所示。

```powershell
# Read Service entries from CSV to enter into Palo Alto
$csvImport = Get-Content ${PSScriptRoot}/AzureSpringCloudUrls.csv | ConvertFrom-Csv

# Convert name column of CSV to add to the Custom URL Group in Palo Alto
$requestBody = @{ 'entry' = [ordered] @{
        '@name' = 'AzureSpringCloud_SG'
        'list'  = @{ 'member' = $csvImport.name }
        'type'  = 'URL List'
    }
} | ConvertTo-Json -Depth 9

$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/CustomURLCategories?location=vsys&vsys=vsys1&name=AzureSpringCloud_SG"

try {
    $existingObject = Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
}
catch {
}

Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body $requestBody -Verbose
```

## <a name="create-a-security-rule"></a>创建安全规则

接下来，创建一个 JSON 文件，以包含安全规则。 将文件命名为 SecurityRule.json 并添加以下内容。 `Trust` 和 `UnTrust` 两个区域的名称与之前在[配置 Palo Alto](#configure-palo-alto) 部分中描述的区域名称相匹配。 `service/member` 条目包含在前面步骤中创建的服务组的名称。

```json
{
    "entry": [
        {
            "@name": "azureSpringCloudRule",
            "@location": "vsys",
            "@vsys": "vsys1",
            "to": {
                "member": [
                    "UnTrust"
                ]
            },
            "from": {
                "member": [
                    "Trust"
                ]
            },
            "source-user": {
                "member": [
                    "any"
                ]
            },
            "application": {
                "member": [
                    "any"
                ]
            },
            "service": {
                "member": [
                    "AzureSpringCloud_SG"
                ]
            },
            "hip-profiles": {
                "member": [
                    "any"
                ]
            },
            "action": "allow",
            "category": {
                "member": [
                    "any"
                ]
            },
            "source": {
                "member": [
                    "any"
                ]
            },
            "destination": {
                "member": [
                    "any"
                ]
            }
        }
    ]
}
```

现在，将此规则应用于 Palo Alto，如下例所示。

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=azureSpringCloudRule"

# Delete the rule if it already exists
try {
    $getResult = Invoke-RestMethod -Headers $paloAltoHeaders -Method Get -SkipCertificateCheck -Uri $url -Verbose
    if ($getResult.'@status' -eq 'success') {
        Invoke-RestMethod -Method Delete  -Headers $paloAltoHeaders -SkipCertificateCheck -Uri $url
    }
}
catch {}

# Create the rule from the JSON file
Invoke-WebRequest -Uri $url -Method Post -Headers $paloAltoHeaders -Body (Get-Content SecurityRule.json) -SkipCertificateCheck
```

## <a name="create-azure-monitor-addresses"></a>创建 Azure Monitor 地址

接下来，使用 AzureMonitorAddresses.csv 文件在 Palo Alto 中定义地址对象。 以下示例代码展示了如何自动执行此任务。

```powershell
Get-Content ./AzureMonitorAddresses.csv | ConvertFrom-Csv | ForEach-Object {
    $requestBody = @{ 'entry' = [ordered]@{
            '@name' = $_.name
            $_.type = $_.address
            'tag'   = @{ 'member' = @($_.tag) }
        }
    }

    $name = $requestBody.entry.'@name'
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Addresses?location=vsys&vsys=vsys1&name=${name}"

    # Delete the address if it already exists
    try {
        Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    }
    catch {
    }

    # Create the address
    Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 3) -Verbose
}
```

## <a name="commit-changes-to-palo-alto"></a>将更改提交到 Palo Alto

必须提交上述一些更改，以便它们变为活动状态。 可使用以下 REST API 调用执行此操作。

```powershell
$url = "https://${PaloAltoIpAddress}/api/?type=commit&cmd=<commit></commit>"
Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
```

## <a name="configure-the-security-rules-for-azure-spring-cloud-subnets"></a>为 Azure Spring Cloud 子网配置安全规则

接下来，添加网络安全规则，以允许来自 Palo Alto 的流量访问 Azure Spring Cloud。 以下示例引用由引用体系结构创建的分支网络安全组 (NSG)：`nsg-spokeapp` 和 `nsg-spokeruntime`。

在 PowerShell 窗口中运行以下 Azure CLI 命令，为每个 NSG 创建必要的网络安全规则，其中 `$PaloAltoAddressPrefix` 是 Palo Alto 的专用 IP 的无类别域际路由选择 (CIDR) 地址。

```azurecli
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeapp' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeruntime' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
```

## <a name="configure-the-next-hop"></a>配置下一个跃点

配置 Palo Alto 后，请将 Azure Spring Cloud 配置为将 Palo Alto 作为其下一个跃点，以进行出站 Internet 访问。 可在 PowerShell 窗口中使用以下 Azure CLI 命令来进行此配置。 请确保为以下变量提供值：

* `$AppResourceGroupName`：包含 Azure Spring Cloud 的资源组的名称。
* `$AzureSpringCloudServiceSubnetRouteTableName`：Azure Spring Cloud 服务/运行时子网路由表的名称。 在引用体系结构中，这设置为 `rt-spokeruntime`。
* `$AzureSpringCloudAppSubnetRouteTableName`：Azure Spring Cloud 应用子网路由表的名称。 在引用体系结构中，这设置为 `rt-spokeapp`。

```azurecli
az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudServiceSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose

az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudAppSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose
```

现在配置已完成。

## <a name="next-steps"></a>后续步骤

* [实时流式传输 Azure Spring Cloud 应用日志](/azure/spring-cloud/how-to-log-streaming)
* [Azure Spring Cloud 中的 Application Insights Java 进程内代理](/azure/spring-cloud/how-to-application-insights)
* [自动化到 Azure Spring Cloud 的应用程序部署](/azure/spring-cloud/how-to-cicd)
