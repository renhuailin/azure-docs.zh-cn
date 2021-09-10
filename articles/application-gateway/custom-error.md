---
title: 创建 Azure 应用程序网关自定义错误页
description: 本文展示了如何创建 Azure 应用程序网关自定义错误页。 你可以在自定义错误页上使用自己的品牌和布局。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bdae2055f46f6f933325c95b86d427951c6cfbc
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222669"
---
# <a name="create-application-gateway-custom-error-pages"></a>创建应用程序网关自定义错误页

应用程序网关允许你创建自定义错误页而非显示默认错误页。 你可以在自定义错误页上使用自己的品牌和布局。

例如，你可以创建当 Web 应用程序无法访问时要显示的维护页面。 另外，还可以创建当有恶意请求发送到 Web 应用程序时要显示的未经授权的访问页面。

以下两个场景支持自定义错误页：

- **“维护”页** - 此自定义错误页用来替代 502 网关错误页。 当应用程序网关没有可将流量路由到的后端时，将显示此错误页。 例如，当存在计划的维护时，或者当无法预料的问题影响了后端池访问时。
- **“未经授权的访问”页** - 此自定义错误页用来替代 403 未经授权的访问页。 当应用程序网关 WAF 检测到恶意流量并阻止了它时，将显示此错误页。

如果某个错误源自后端服务器，则会将该错误不加修改地传送回调用方。 不会显示自定义错误页。 当请求无法到达后端时，应用程序网关可以显示自定义错误页。

可以在全局级别和侦听器级别定义自定义错误页：

- **全局级别** - 此错误页适用于在该应用程序网关上部署的所有 Web 应用程序的流量。
- **侦听器级别** - 此错误页适用于在该侦听器上收到的流量。
- **两者** - 在侦听器级别定义的自定义错误页会覆盖在全局级别设置的自定义错误页。

若要创建自定义错误页，你必须具有：

- HTTP 响应状态代码。
- 错误页的相应位置。 
- 该位置的可公开访问的 Azure 存储 blob。
- *.htm 或 *.html 扩展类型。 

错误页的大小必须小于 1 MB。 可以引用此 HTML 文件的内部或外部图像/CSS。 对于在外部引用的资源，请使用可公开访问的绝对 URL。 在使用内部图像（Base64 编码的内联图像）或 CSS 时，请注意 HTML 文件大小。 目前不支持为同一 blob 位置中的文件使用相对链接。

在你指定错误页后，应用程序网关会从存储 blob 位置下载它并将其保存到本地应用程序网关缓存中。 然后，该 HTML 页由应用程序网关提供，而外部引用的资源则由客户端直接提取。 若要修改现有的自定义错误页，必须在应用程序网关配置中指向一个不同的 blob 位置。 应用程序网关不会定期检查 blob 位置来提取新版本。

## <a name="portal-configuration"></a>门户配置

1. 在门户中导航到“应用程序网关”，并选择一个应用程序网关。

    ![显示应用程序网关的“概述”页的屏幕截图。](media/custom-error/ag-overview.png)
2. 单击“侦听器”并导航到你要在其中指定错误页的特定侦听器。

    ![应用程序网关侦听器](media/custom-error/ag-listener.png)
3. 在侦听器级别为 403 WAF 错误或 502 维护页配置一个自定义错误页。

    > [!NOTE]
    > 目前不支持从 Azure 门户中创建全局级别的自定义错误页。

4. 为给定的错误状态代码指定一个可公开访问的 blob URL，然后单击“保存”。 现在，应用程序网关已经配置了自定义错误页。

   ![应用程序网关错误代码](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell 配置

可使用 Azure PowerShell 来配置自定义缩放页。 例如，全局自定义错误页：

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

或侦听器级别错误页：

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

有关详细信息，请参阅 [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) 和 [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror)。

## <a name="next-steps"></a>后续步骤

有关应用程序网关诊断的信息，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。
