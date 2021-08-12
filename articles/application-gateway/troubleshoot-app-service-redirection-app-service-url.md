---
title: 排查重定向到应用服务 URL 的问题
titleSuffix: Azure Application Gateway
description: 本文介绍如何排查将 Azure 应用程序网关与 Azure 应用服务配合使用时出现的重定向问题
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 83ad2e7a8f138451063eef1746555563970e125e
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110681805"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>排查应用程序网关中的应用服务问题

了解如何诊断并解决将 Azure 应用服务用作后端目标时 Azure 应用程序网关可能出现的问题。

## <a name="overview"></a>概述

本文介绍如何排查以下问题：

* 发生重定向时，应用服务 URL 在浏览器中公开。
* 应用服务 ARRAffinity Cookie 域设置为应用服务主机名 (example.azurewebsites.net)，而不是原始主机。

当后端应用程序发送重定向响应时，你可能希望将客户端重定向到不同的 URL，而不是后端应用程序指定的 URL。 当应用服务托管在应用程序网关后面，并要求客户端重定向到其相对路径时，你可能希望这样做。 例如，从 contoso.azurewebsites.net/path1 重定向到 contoso.azurewebsites.net/path2。 

当应用服务发送重定向响应时，它会在其响应的位置标头中，使用它从应用程序网关收到的请求中的相同主机名。 例如，客户端将直接向 contoso.azurewebsites.net/path2 发出请求，而不是通过应用程序网关 contoso.com/path2 发出请求。 你不希望绕过应用程序网关。

此问题可能是以下主要原因造成的：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure Active Directory 身份验证导致重定向。

此外，在应用程序网关后使用应用服务时，与应用程序网关关联的域名 (example.com) 不同于应用服务的域名（例如 example.azurewebsites.net）。 应用服务设置的 ARRAffinity Cookie 域值携带 example.azurewebsites.net 域名，这不符合需要。 原始主机名 example.com 应是 Cookie 中的域名值。

## <a name="sample-configuration"></a>示例配置

- HTTP 侦听器：基本或多站点
- 后端地址池：应用服务
- HTTP 设置：已启用“从后端地址中选取主机名”
- 探测：已启用“从 HTTP 设置中选取主机名”

## <a name="cause"></a>原因

应用服务是多租户服务，因此它会使用请求中的主机标头将请求路由到正确的终结点。 应用服务的默认域名 *.azurewebsites.net（例如 contoso.azurewebsites.net）不同于应用程序网关的域名（例如 contoso.com）。 

来自客户端的原始请求包含应用程序网关的域名 contoso.com 作为主机名。 需要配置应用程序网关，以便在将请求路由到应用服务后端时，将原始请求中的主机名更改为应用服务的主机名。 在应用程序网关的 HTTP 设置配置中使用开关“从后端地址中选取主机名”。 在运行状况探测配置中使用开关“从后端 HTTP 设置中选取主机名”。



![应用程序网关更改主机名](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

除非另行配置，否则应用服务在执行重定向时，将使用 location 标头中重写的主机名 contoso.azurewebsites.net，而不是原始主机名 contoso.com。 检查以下示例请求和响应标头。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
在以上示例中，可以看到响应标头包含 301 重定向状态代码。 location 标头包含应用服务的主机名，而不是原始主机名 `www.contoso.com`。

## <a name="solution-rewrite-the-location-header"></a>解决方案：重写 location 标头

将 location 标头中的主机名设置为应用程序网关的域名。 要执行此操作，请创建一个[重写规则](./rewrite-http-headers-url.md)，其中包含评估响应的 location 标头是否包含 azurewebsites.net 的条件。 该规则还必须执行相应的操作来重写 location 标头，使其包含应用程序网关的主机名。 有关详细信息，请参阅有关[如何重写 location 标头](./rewrite-http-headers-url.md#modify-a-redirection-url)的说明。

> [!NOTE]
> HTTP 标头重写支持仅适用于应用程序网关的 [Standard_v2 和 WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md)。 建议[迁移到 v2](./migrate-v1-v2.md)，以获得标头重写和其他 v2 SKU [的高级功能](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)。

## <a name="alternate-solution-use-a-custom-domain-name"></a>替代解决方案：使用自定义域名

另一种解决方案是使用应用服务的“自定义域”功能，始终将流量重定向到应用程序网关域名（在示例中为 `www.contoso.com`）。 此配置还可以作为 ARR 相关性 Cookie 问题的解决方案。 默认情况下，ARRAffinity Cookie 域设置为应用服务的默认主机名 (example.azurewebsites.net)，而不是应用程序网关的域名。 因此，在这种情况下，浏览器将由于请求域名和 Cookie 不同而拒绝 Cookie。

可以按照指定方法来处理重定向和 ARRAffinity 的 Cookie 域不匹配问题。 此方法需要你拥有自定义域的 DNS 区域访问权限。

**步骤 1**：在应用服务中设置自定义域，并通过添加 [CNAME & TXT DNS 记录](../app-service/app-service-web-tutorial-custom-domain.md#3-get-a-domain-verification-id)来验证域所有权。
记录看起来类似于
-  `www.contoso.com` IN CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` IN TXT "`<verification id string>`"


**步骤 2**：仅域验证需要上一步中的 CNAME 记录。 最后需要通过应用程序网关路由的流量。 因此，可以立即修改 `www.contoso.com` 的CNAME 以指向应用程序网关的 FQDN。 要为应用程序网关设置 FQDN，请导航到其公共 IP 地址资源并为其分配“DNS 名称标签”。 更新后的 CNAME 记录现在应如下所示 
-  `www.contoso.com` IN CNAME `contoso.eastus.cloudapp.azure.com`


**步骤 3**：为关联的 HTTP 设置禁用“从后端地址中选择主机名”。

在 PowerShell 中，请勿在 `Set-AzApplicationGatewayBackendHttpSettings` 命令中使用 `-PickHostNameFromBackendAddress` 开关。


**步骤 4**：为使探测确定后端是否正常运行和操作流量，请将自定义“主机的运行状况探测”字段设置为应用服务的自定义域或默认域。

在 PowerShell 中，请勿在 `Set-AzApplicationGatewayProbeConfig` 命令中使用 `-PickHostNameFromBackendHttpSettings` 开关，改为在探测的 -HostName 开关中使用应用服务的自定义域或默认域。

若要使用 PowerShell 对现有设置执行上述步骤，请使用以下示例 PowerShell 脚本。 请注意，我们没有在探测和 HTTP 设置配置中使用 **-PickHostname** 开关。

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。
