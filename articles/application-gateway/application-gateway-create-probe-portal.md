---
title: 使用门户创建自定义探测
titleSuffix: Azure Application Gateway
description: 了解如何使用门户创建应用程序网关的自定义探测
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 1244cc0fdd9a5c978ee64c1aa7ce4af20272c818
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634438"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>使用门户创建应用程序网关的自定义探测

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 经典 PowerShell](application-gateway-create-probe-classic-ps.md)

本文介绍如何通过 Azure 门户向现有应用程序网关添加自定义运行状况探测。 Azure 应用程序网关使用运行状况探测来监视后端池中资源的运行状况。

## <a name="before-you-begin"></a>准备阶段

如果还没有应用程序网关，请访问[创建应用程序网关](./quick-create-portal.md)，创建要使用的应用程序网关。

## <a name="create-probe-for-application-gateway-v2-sku"></a>为应用程序网关 v2 SKU 创建探测

可通过门户分两步配置探测。 第一个步骤是输入探测配置所需的值。 第二个步骤使用此探测配置测试后端运行状况，然后保存探测。 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>输入探测属性

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[一个月免费试用版](https://azure.microsoft.com/free)

2. 在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”边栏选项卡中单击应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“partners.contoso.net”， 轻松访问应用程序网关。

3. 选择“运行状况探测”，然后选择“添加”以添加新的运行状况探测。  

   ![添加新探测][4]

4. 在“添加运行状况探测”页上填写探测的所需信息，并在完成后选择“确定”。  

   |**设置** | **值** | **详细信息**|
   |---|---|---|
   |**名称**|customProbe|此值是可在门户中访问的探测的易记名称。|
   |**协议**|HTTP 或 HTTPS | 运行状况探测使用的协议。 |
   |**主机**|例如 contoso.com|此值是应用程序服务器上运行的虚拟主机的名称（不同于 VM 主机名）。 会将探测发送到 \<protocol\>://\<host name\>:\<port\>/\<urlPath\>：这也可以是服务器的专用IP，也可以是公共 IP 地址，也可以是公共 IP 地址的 DNS 条目。  当与基于文件的路径条目一起使用时，将尝试访问服务器，并以健康状况检查的形式来验证服务器上确实存在特定文件。|
   |**从后端 HTTP 设置中选取主机名**|是或否|将探测中的主机头设置为与此探测关联的 HTTP 设置中的主机名。 对于 Azure 应用服务等多租户后端，特别需要注意提供正确的值。 [了解详细信息](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**从后端 HTTP 设置中选取端口**| 是或否|将运行状况探测的端口设置为与此探测关联的 HTTP 设置中的端口。 如果选择“否”，则可以输入要使用的自定义目标端口 |
   |**端口**| 1-65535 | 用于运行状况探测的自定义端口 | 
   |**路径**|/或任何有效的路径|自定义探测的完整 URL 的其余部分。 有效路径以“/”开头。 对于 http:\//contoso.com 的默认路径，只需使用“/”。  对于静态健康状况检查（非基于 Web），还可以输入文件的服务器路径。  使用公共/专用 IP 或公共 IP DNS 条目作为主机名条目时，应使用文件路径。|
   |**间隔(秒)**|30|运行探测来检查运行状况的频率。 建议不要将时间间隔设置为短于 30 秒。|
   |**超时(秒)**|30|超时之前探测的等待时间。如果在此超时期间内未收到有效响应，则将探测标记为失败。 超时间隔必须足够长，以便进行 http 调用，确保后端运行状况页可用。 请注意，超时值不能大于此探测设置中使用的“间隔”值，或者要与此探测关联的 HTTP 设置中的“请求超时”值。|
   |**不正常阈值**|3|系统认为不正常的连续失败尝试次数。 阈值可以设置为 1 或更大值。|
   |**使用匹配条件的探测**|是或否|默认情况下，状态代码为 200 到 399 的 HTTP(S) 响应被视为正常。 可以更改后端响应代码或后端响应正文的可接受范围。 [了解详细信息](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP 设置**|下拉列表中的选项|探测将与此处选择的 HTTP 设置相关联，因此，它会监视与所选 HTTP 设置关联的后端池的运行状况。 它将对探测请求使用所选 HTTP 设置中所用的同一端口。 只能选择不与任何其他自定义探测关联的 HTTP 设置。 <br>请注意，只有这些 HTTP 设置可用于关联，它们的协议与在此探测配置中选择的协议相同，并且采用相同的“从后端 HTTP 设置中选取主机名”开关状态。|
   
   > [!IMPORTANT]
   > 仅当探测已关联到一个或多个 HTTP 设置时，它才会监视后端的运行状况。 它会监视已关联到与其自身关联的 HTTP 设置的后端池的后端资源。 探测请求将作为 \<protocol\>://\<hostName\>:\<port\>/\<urlPath\> 发送。

### <a name="test-backend-health-with-the-probe"></a>使用探测测试后端运行状况

输入探测属性后，可以测试后端资源的运行状况，以验证探测配置是否正确，并且后端资源是否按预期运行。

1. 选择“测试”并记下探测结果。 应用程序网关将测试后端池中所有后端资源的运行状况，这些池已关联到用于此探测的 HTTP 设置。 

   ![测试后端运行状况][5]

2. 如果存在任何不正常的后端资源，请查看“详细信息”列来了解资源处于不正常状态的原因。 如果由于探测配置错误而将资源标记为不正常，请选择“返回探测”链接并编辑探测配置。 否则，如果由于后端问题而将该资源标记为不正常，请解决后端资源问题，然后依次选择“返回探测”链接、“测试”再次测试后端。 

   > [!NOTE]
   > 即使后端资源不正常，也可以选择保存探测，但不建议这样做。 这是因为，应用程序网关不会将请求转发到后端池中被探测判定为运行不正常的后端服务器。 如果后端池中没有任何正常的资源，则无法访问应用程序，并会出现 HTTP 502 错误。

   ![查看探测结果][6]

3. 选择“添加”以保存探测。 

## <a name="create-probe-for-application-gateway-v1-sku"></a>为应用程序网关 v1 SKU 创建探测

可通过门户分两步配置探测。 第一步是创建探测。 第二步是将探测添加到应用程序网关的后端 http 设置。

### <a name="create-the-probe"></a><a name="createprobe"></a>创建探测

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[一个月免费试用版](https://azure.microsoft.com/free)

2. 在 Azure 门户上的“收藏夹”窗格中，选择“所有资源”。 在“所有资源”页中选择应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“partners.contoso.net”， 轻松访问应用程序网关。

3. 选择“探测”，然后选择“添加”以添加探测。 

   ![添加填写了信息的“探测”边栏选项卡][1]

4. 在“添加运行状况探测”边栏选项卡上填写探测的所需信息，完成后选择“确定”。 

   |**设置** | **值** | **详细信息**|
   |---|---|---|
   |**名称**|customProbe|此值是可在门户中访问的探测的易记名称。|
   |**协议**|HTTP 或 HTTPS | 运行状况探测使用的协议。 |
   |**主机**|例如 contoso.com|此值是应用程序服务器上运行的虚拟主机的名称（不同于 VM 主机名）。 探测发送到 (协议)://(主机名):(http 设置中的端口)/urlPath。  仅当应用程序网关上配置了多站点时，此项设置才适用。 如果应用程序网关是为单个站点配置的，请输入“127.0.0.1”。  对于静态健康状况检查（非基于 Web），还可以输入文件的服务器路径。 使用公共/专用 IP 或公共 IP DNS 条目作为主机名条目时，应使用文件路径。|
   |**从后端 HTTP 设置中选取主机名**|是或否|将探测中的 *host* 标头设置为与此探测关联到的 HTTP 设置相关联的后端池中后端资源的主机名。 对于 Azure 应用服务等多租户后端，特别需要注意提供正确的值。 [了解详细信息](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**路径**|/或任何有效的路径|自定义探测的完整 URL 的其余部分。 有效路径以“/”开头。 对于 http:\//contoso.comm 的默认路径，只需使用“/”。对于静态健康状况检查（非基于 web），也可以输入文件的服务器路径。  使用公共/专用 IP 或公共 IP DNS 条目作为主机名条目时，应使用文件路径。|
   |**间隔(秒)**|30|运行探测来检查运行状况的频率。 建议不要将时间间隔设置为短于 30 秒。|
   |**超时(秒)**|30|超时之前探测的等待时间。如果在此超时期间内未收到有效响应，则将探测标记为失败。 超时间隔必须足够长，以便进行 http 调用，确保后端运行状况页可用。 请注意，超时值不能大于此探测设置中使用的“间隔”值，或者要与此探测关联的 HTTP 设置中的“请求超时”值。|
   |**不正常阈值**|3|系统认为不正常的连续失败尝试次数。 阈值可以设置为 1 或更大值。|
   |**使用匹配条件的探测**|是或否|默认情况下，状态代码为 200 到 399 的 HTTP(S) 响应被视为正常。 可以更改后端响应代码或后端响应正文的可接受范围。 [了解详细信息](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > 主机名不同于服务器名。 此值是运行在应用程序服务器上的虚拟主机的名称。 探测将发送到 \<protocol\>://\<hostName\>:\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>向网关添加探测

创建探测以后，即可将其添加到网关。 探测设置在应用程序网关的后端 http 设置中设置。

1. 单机应用程序网关的“HTTP 设置”，然后单击窗口中列出的当前后端 http 设置，以便显示“配置”边栏选项卡。

   ![https 设置窗口][2]

2. 在“appGatewayBackEndHttpSettings”设置页上，选中“使用自定义探测”复选框，然后在“自定义探测”下拉列表中选择在[创建探测](#createprobe)部分创建的探测。  
   完成后，单击“保存”即可应用相关设置。

## <a name="next-steps"></a>后续步骤

使用[后端运行状况视图](./application-gateway-diagnostics.md#back-end-health)查看探测确定的后端资源运行状况。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
