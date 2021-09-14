---
title: 如何在包含 Azure 应用程序网关的虚拟网络中使用 API 管理
titleSuffix: Azure API Management
description: 在使用应用程序网关（Web 应用程序防火墙）作为前端的内部虚拟网络中设置和配置 Azure API 管理
services: api-management
documentationcenter: ''
author: solankisamir
ms.service: api-management
ms.topic: how-to
ms.author: sasolank
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell,contperf-fy21q4
ms.openlocfilehash: e7c4583797719ca3b9cbb47d5dead48138d22fe1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543101"
---
# <a name="integrate-api-management-in-an-internal-virtual-network-with-application-gateway"></a>将内部虚拟网络中的 API 管理与应用程序网关集成

可在[处于内部模式的虚拟网络](api-management-using-with-internal-vnet.md)中配置 API 管理服务，以便只能从该虚拟网络内部访问该服务。 [Azure 应用程序网关](../application-gateway/overview.md)是一种 PaaS 服务，充当第 7 层负载均衡器。 该网关充当反向代理服务，并提供 Web 应用程序防火墙 (WAF) 及其他产品/服务。

通过将内部虚拟网络中预配的 API 管理与应用程序网关前端相结合，可以：

* 使用同时供内部使用者和外部使用者使用的相同 API 管理资源。
* 使用单个 API 管理资源，并向外部使用者提供在 API 管理中定义的一部分 API。
* 提供配套的方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

> [!NOTE]
> 本文已更新为使用[应用程序网关 WAF_v2 SKU](../application-gateway/application-gateway-autoscaling-zone-redundant.md)。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要执行本文中所述的步骤，必须具有：

* 一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 证书
     - 适用于 API 管理服务自定义主机名的 PFX 文件：网关、开发人员门户和管理终结点。 
     - 适用于 PFX 根证书的 CER 文件。 
     
    有关详细信息，请参阅[后端证书](../application-gateway/certificates-for-backend-authentication.md)。 出于测试目的，可以选择性生成[自签名证书](../application-gateway/self-signed-certificates.md)。
* 最新版本的 Azure PowerShell。 [安装 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。

## <a name="scenario"></a>方案

本文介绍如何对内部和外部使用者使用单个 API 管理服务，并使其充当本地和云 API 的单一前端。 另外，本文还介绍了如何使用应用程序网关中提供的路由功能，仅公开一部分 API（在示例中以绿色突出显示）供外部使用。

在第一个设置示例中，只能从虚拟网络内部管理所有 API。 内部使用者（以橙色突出显示）可以访问所有内部和外部 API。 流量永远不会外发到 Internet。 可以通过快速路由线路提供高性能连接。

![url 路由](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

### <a name="what-is-required-to-integrate-api-management-and-application-gateway"></a>集成 API 管理和应用程序网关需要什么？

* 后端服务器池：API 管理服务的内部虚拟 IP 地址。
* **后端服务器池设置：** 每个池具有端口、协议和基于 Cookie 的相关性等设置。 这些设置将应用到池中的所有服务器。
* **前端端口：** 此端口是应用程序网关上打开的公共端口。 抵达此端口的流量将重定向到后端服务器之一。
* **侦听器：** 侦听器具有前端端口、协议（Http 或 Https，这些值区分大小写）和 TLS/SSL 证书名称（如果配置 TLS 卸载）。
* 规则：规则将侦听器绑定到后端服务器池。
* 自定义运行状况探测：默认情况下，应用程序网关使用基于 IP 地址的探测来判断 BackendAddressPool 中的哪些服务器处于活动状态。 API 管理服务只响应包含正确主机标头的请求，因此默认的探测会失败。 需要定义一个自定义运行状况探测，帮助应用程序网关确定服务是否处于活动状态，以及是否应该转发请求。
* **自定义域证书：** 若要从 Internet 访问 API 管理，需要创建从服务主机名到应用程序网关前端 DNS 名称的 CNAME 映射。 这可以确保发送到应用程序网关，并转发到 API 管理的主机名标头和证书是 API 管理可以识别为有效的对象。 在此示例中，我们将使用三个证书，分别用于 API 管理服务的网关（后端）、开发人员门户和管理终结点。  

## <a name="steps-required-to-integrate-api-management-and-application-gateway"></a>集成 API 管理和应用程序网关所需执行的步骤

1. 创建 Resource Manager 的资源组。
1. 创建应用程序网关的虚拟网络、子网和公共 IP。 为 API 管理创建另一个子网。
1. 在上一步创建的虚拟网络子网中创建 API 管理服务。 确保使用“内部”模式。
1. 在 API 管理服务中设置自定义域名。
1. 为虚拟网络中的 DNS 解析配置专用 DNS 区域
1. 创建应用程序网关配置对象。
1. 创建应用程序网关资源。
1. 创建从应用程序网关公共 DNS 名称到 API 管理代理主机名的 CNAME 映射。

### <a name="expose-the-developer-portal-and-management-endpoint-externally-through-application-gateway"></a>通过应用程序网关向外部公开开发人员门户和管理终结点

在本指南中，我们还会通过应用程序网关向外部用户公开开发人员门户和管理终结点。 需要执行额外的步骤来为每个终结点创建侦听器、探测、设置和规则。 相应步骤中提供了所有详细信息。

> [!WARNING]
> 如果使用 Azure AD 或第三方身份验证，请在应用程序网关中启用[基于 cookie 的会话亲和性](../application-gateway/features.md#session-affinity)功能。

> [!WARNING]
> 为防止应用程序网关 WAF 中断开发人员门户中 OpenAPI 规范的下载，需要禁用防火墙规则 `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`。
> 
> 可能会中断门户功能的应用程序网关 WAF 规则包括：
> 
> - 适用于管理模式的 `920300`、`920330`、`931130`、`942100`、`942110`、`942180`、`942200`、`942260`、`942340`、`942370`
> - 适用于已发布门户的 `942200`、`942260`、`942370`、`942430`、`942440`

## <a name="create-a-resource-group-for-resource-manager"></a>创建 Resource Manager 的资源组

### <a name="step-1"></a>步骤 1

登录 Azure

```powershell
Connect-AzAccount
```

使用凭据进行身份验证。

### <a name="step-2"></a>步骤 2

选择所需的订阅。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>步骤 3

创建资源组（如果要使用现有的资源组，请跳过此步骤）。

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager 要求所有资源组指定一个位置。 此位置用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>为应用程序网关创建虚拟网络和子网

以下示例演示如何使用资源管理器创建虚拟网络。 本示例中的虚拟网络由应用程序网关和 API 管理的单独子网组成。

### <a name="step-1"></a>步骤 1

为应用程序网关和 API 管理子网创建网络安全组和 NSG 规则。

```powershell
$appGwRule1 = New-AzNetworkSecurityRuleConfig -Name appgw-in -Description "AppGw inbound" `
    -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix `
    GatewayManager -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 65200-65535
$appGwNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APPGW" -SecurityRules $appGwRule1

$apimRule1 = New-AzNetworkSecurityRuleConfig -Name apim-in -Description "APIM inbound" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix `
    ApiManagement -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 3443
$apimNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APIM" -SecurityRules $apimRule1
```

### <a name="step-2"></a>步骤 2

将地址范围 10.0.0.0/24 分配到创建虚拟网络时用于应用程序网关的子网变量。

```powershell
$appGatewaySubnet = New-AzVirtualNetworkSubnetConfig -Name "appGatewaySubnet" -NetworkSecurityGroup $appGwNsg -AddressPrefix "10.0.0.0/24"
```

### <a name="step-3"></a>步骤 3

将地址范围 10.0.1.0/24 分配到创建虚拟网络时用于 API 管理的子网变量。

```powershell
$apimSubnet = New-AzVirtualNetworkSubnetConfig -Name "apimSubnet" -NetworkSecurityGroup $apimNsg -AddressPrefix "10.0.1.0/24"
```

### <a name="step-4"></a>步骤 4

在美国西部地区的资源组“apim-appGw-RG”中创建名为“appgwvnet”的虚拟网络。 使用前缀 10.0.0.0/16 以及子网 10.0.0.0/24 和 10.0.1.0/24。

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName `
  -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appGatewaySubnet,$apimSubnet
```

### <a name="step-5"></a>步骤 5

分配子网变量，以供后续步骤使用

```powershell
$appGatewaySubnetData = $vnet.Subnets[0]
$apimSubnetData = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-virtual-network-configured-in-internal-mode"></a>在以内部模式配置的虚拟网络中创建 API 管理服务

以下示例演示如何在配置为仅供内部访问的虚拟网络中创建 API 管理服务。

### <a name="step-1"></a>步骤 1

使用前面创建的子网 `$apimSubnetData` 创建 API 管理虚拟网络对象。

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimSubnetData.Id
```

### <a name="step-2"></a>步骤 2

在虚拟网络中创建 API 管理服务。 此示例在“开发人员服务”层中创建服务。 替换 API 管理服务的唯一名称。

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name, must be globally unique
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

在此层中创建和激活 API 管理服务可能需要 30 到 40 分钟。 上述命令成功后，请参阅[访问内部虚拟网络 API 管理服务所需的 DNS 配置](api-management-using-with-internal-vnet.md#dns-configuration)来确认可访问该服务。 

## <a name="set-up-custom-domain-names-in-api-management"></a>在 API 管理中设置自定义域名

### <a name="step-1"></a>步骤 1

使用带有域私钥的证书和受信任的根证书的详细信息初始化以下变量。 本示例使用 `api.contoso.net`、`portal.contoso.net` 和 `management.contoso.net`。  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$managementHostname = "management.contoso.net"               # API management endpoint host
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$managementCertPfxPath = "C:\Users\Contoso\management.pfx"   # full path to management.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate
$managementCertPfxPassword = "certificatePassword123"    # password for management.contoso.net pfx certificate
# Path to trusted root CER file used in Application Gateway HTTP settings
$trustedRootCertCerPath = "C:\Users\Contoso\trustedroot.cer" # full path to contoso.net trusted root .cer file

$certGatewayPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
$certManagementPwd = ConvertTo-SecureString -String $managementCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>步骤 2

为 API 管理终结点创建和设置主机名配置对象。  

```powershell
$gatewayHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname `
  -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certGatewayPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname `
  -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd
$managementHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $managementHostname `
  -HostnameType Management -PfxPath $managementCertPfxPath -PfxPassword $certManagementPwd

$apimService.ProxyCustomHostnameConfiguration = $gatewayHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
$apimService.ManagementCustomHostnameConfiguration = $managementHostnameConfig

Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> 若要配置与旧版开发人员门户的连接，需要将 `-HostnameType DeveloperPortal` 替换为 `-HostnameType Portal`。

## <a name="configure-a-private-zone-for-dns-resolution-in-the-virtual-network"></a>为虚拟网络中的 DNS 解析配置专用区域

### <a name="step-1"></a>步骤 1

创建专用 DNS 区域并链接虚拟网络。

```powershell
$myZone = New-AzPrivateDnsZone -Name "contoso.net" -ResourceGroupName $resGroupName 
$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Name "mylink" `
  -VirtualNetworkId $vnet.id
```

### <a name="step-2"></a>步骤 2

为自定义域主机名创建 A 记录，映射到 API 管理服务的专用 IP 地址：

```powershell
$apimIP = $apimService.PrivateIPAddresses[0]

New-AzPrivateDnsRecordSet -Name api -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name portal -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name management -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>创建前端配置的公共 IP 地址

在资源组中，创建标准公共 IP 资源 publicIP01。

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName `
  -name "publicIP01" -location $location -AllocationMethod Static -Sku Standard
```

服务启动时，会将一个 IP 地址分配到应用程序网关。

## <a name="create-application-gateway-configuration"></a>创建应用程序网关配置

在创建应用程序网关之前，必须设置所有配置项目。 以下步骤会创建应用程序网关资源所需的配置项目。

### <a name="step-1"></a>步骤 1

创建名为“gatewayIP01”  的应用程序网关 IP 配置。 当应用程序网关启动时，其会从配置的子网获取 IP 地址，再将网络流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appGatewaySubnetData
```

### <a name="step-2"></a>步骤 2

配置公共 IP 终结点的前端 IP 端口。 此端口是最终用户连接到的端口。

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>步骤 3

使用公共 IP 终结点配置前端 IP。

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>步骤 4

为应用程序网关配置用于解密和重新加密所传递流量的证书。

> [!NOTE]
> 应用程序网关支持定义自定义 TLS 选项、禁用某些 TLS 协议版本，以及指定密码套件和首选项顺序。 若要详细了解可配置的 TLS 选项，请参阅 [TLS 策略概述](../application-gateway/application-gateway-ssl-policy-overview.md)。

```powershell
$certGateway = New-AzApplicationGatewaySslCertificate -Name "gatewaycert" `
  -CertificateFile $gatewayCertPfxPath -Password $certGatewayPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "portalcert" `
  -CertificateFile $portalCertPfxPath -Password $certPortalPwd
$certManagement = New-AzApplicationGatewaySslCertificate -Name "managementcert" `
  -CertificateFile $managementCertPfxPath -Password $certManagementPwd
```

### <a name="step-5"></a>步骤 5

为应用程序网关创建 HTTP 侦听器。 为其分配前端 IP 配置、端口和 TLS/SSL 证书。

```powershell
$gatewayListener = New-AzApplicationGatewayHttpListener -Name "gatewaylistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certGateway -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "portallistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
$managementListener = New-AzApplicationGatewayHttpListener -Name "managementlistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certManagement -HostName $managementHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>步骤 6

为 API 管理服务的 `ContosoApi` 网关域终结点创建自定义探测。 路径 `/status-0123456789abcdef` 是所有 API 管理服务中托管的默认运行状况终结点。 将 `api.contoso.net` 设置为自定义探测主机名，以便使用 TLS/SSL 证书保护它。

> [!NOTE]
> 主机名 `contosoapi.azure-api.net` 是在公共 Azure 中创建名为 `contosoapi` 的服务时配置的默认代理主机名。
>

```powershell
$apimGatewayProbe = New-AzApplicationGatewayProbeConfig -Name "apimgatewayprobe" `
  -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" `
  -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" `
  -Protocol "Https" -HostName $portalHostname -Path "/signin" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
$apimManagementProbe = New-AzApplicationGatewayProbeConfig -Name "apimmanagementprobe" `
  -Protocol "Https" -HostName $managementHostname -Path "/ServiceStatus" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>步骤 7

上传要在 HTTP 设置上配置的受信任的根证书。

```powershell
$trustedRootCert = New-AzApplicationGatewayTrustedRootCertificate -Name "whitelistcert1" -CertificateFile $trustedRootCertCerPath
```

### <a name="step-8"></a>步骤 8

为应用程序网关配置 HTTP 后端设置，包括后端请求的超时限制，超过该限制后，将取消这些设置。 此值不同于探测超时。

```powershell
$apimPoolGatewaySetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolGatewaySetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimGatewayProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolManagementSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolManagementSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimManagementProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
```

### <a name="step-9"></a>步骤 9

使用每个 API 管理终结点各自的域名为其配置后端 IP 地址池。

```powershell
$apimGatewayBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "gatewaybackend" `
  -BackendFqdns $gatewayHostname
$apimPortalBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "portalbackend" `
  -BackendFqdns $portalHostname
$apimManagementBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "managementbackend" `
  -BackendFqdns $managementHostname
```

### <a name="step-10"></a>步骤 10

为应用程序网关创建规则以使用基本路由。

```powershell
$gatewayRule = New-AzApplicationGatewayRequestRoutingRule -Name "gatewayrule" `
  -RuleType Basic -HttpListener $gatewayListener -BackendAddressPool $apimGatewayBackendPool `
  -BackendHttpSettings $apimPoolGatewaySetting
$portalRule = New-AzApplicationGatewayRequestRoutingRule -Name "portalrule" `
  -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimPortalBackendPool `
  -BackendHttpSettings $apimPoolPortalSetting
$managementRule = New-AzApplicationGatewayRequestRoutingRule -Name "managementrule" `
  -RuleType Basic -HttpListener $managementListener -BackendAddressPool $apimManagementBackendPool `
  -BackendHttpSettings $apimPoolManagementSetting
```

> [!TIP]
> 更改 `-RuleType` 和路由，以限制对开发人员门户某些页面的访问。

### <a name="step-11"></a>步骤 11

配置实例数目和应用程序网关的大小。 本示例使用 [WAF_v2 SKU](../web-application-firewall/ag/ag-overview.md) 以提高 API 管理资源的安全性。

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_v2" -Tier "WAF_v2" -Capacity 2
```

### <a name="step-12"></a>步骤 12

将 WAF 配置为“防护”模式。

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>创建应用程序网关

创建包含前述步骤中所有配置对象的应用程序网关。

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location `
  -BackendAddressPools $apimGatewayBackendPool,$apimPortalBackendPool,$apimManagementBackendPool `
  -BackendHttpSettingsCollection $apimPoolGatewaySetting, $apimPoolPortalSetting, $apimPoolManagementSetting `
  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 `
  -HttpListeners $gatewayListener,$portalListener,$managementListener `
  -RequestRoutingRules $gatewayRule,$portalRule,$managementRule `
  -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $certGateway,$certPortal,$certManagement `
  -TrustedRootCertificate $trustedRootCert -Probes $apimGatewayProbe,$apimPortalProbe,$apimManagementProbe
```

应用程序网关部署完成后，在门户中或通过运行以下命令确认 API 管理后端的运行状况：

```powershell
Get-AzApplicationGatewayBackendHealth -Name $appgwName -ResourceGroupName $resGroupName
```

确保每个后端池的运行状况为“正常”。 如果需要对运行不正常的后端或运行状况未知的后端进行故障排除，请参阅[排查应用程序网关中的后端运行状况问题](../application-gateway/application-gateway-backend-health-troubleshooting.md)。

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>创建从 API 管理代理主机名到应用程序网关资源公共 DNS 名称的 CNAME 映射

创建网关后，请配置前端以进行通信。 使用公共 IP 地址时，应用程序网关需要动态分配的 DNS 名称，这可能不太易于使用。

使用应用程序网关的 DNS 名称创建 CNAME 记录，将 API 管理网关主机名（前面示例中的 `api.contoso.net`）指向此 DNS 名称。 若要配置前端 IP CNAME 记录，可使用 `PublicIPAddress` 元素检索应用程序网关及其关联 IP/DNS 名称的详细信息。 网关重启时 VIP 可能会发生变化，因此不建议使用 A 记录。

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

出于测试目的，可以使用特定条目更新本地计算机上的主机文件，该条目可将应用程序网关的公共 IP 地址映射到所配置的每个 API 管理终结点主机名（例如 `api.contoso.net`、`portal.contoso.net`、`management.contoso.net`）。

## <a name="summary"></a>摘要

虚拟网络中配置的 Azure API 管理为配置的所有 API 提供单个网关接口，而不考虑这些 API 是托管在本地还是云中。 将应用程序网关与 API 管理集成可以灵活地、有选择性地启用可在 Internet 上访问的特定 API，并向 API 管理实例提供 WAF 作为前端。

## <a name="next-steps"></a>后续步骤

* 详细了解 Azure 应用程序网关
  * [应用程序网关概述](../application-gateway/overview.md)
  * [应用程序网关 Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)
  * [使用基于路径的路由的应用程序网关](../application-gateway/tutorial-url-route-powershell.md)
* 详细了解 API 管理和虚拟网络
  * [将 API 管理与内部虚拟网络搭配使用](api-management-using-with-internal-vnet.md)
  * [如何将 API 管理与虚拟网络搭配使用](api-management-using-with-vnet.md)
