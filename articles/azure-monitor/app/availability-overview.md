---
title: Application Insights 可用性测试
description: 设置定期 Web 测试，以监视应用或网站的可用性和响应能力。
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2e87d9eca75cfd507549213999882dfe6ff3ffa6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737089"
---
# <a name="application-insights-availability-tests"></a>Application Insights 可用性测试

部署 Web 应用或网站之后，可以设置重复测试来监视可用性和响应能力。 [Application Insights](./app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果你的应用程序未响应或响应速度太慢，则会发出警报。

对于可以从公共 Internet 访问的任何 HTTP 或 HTTPS 终结点，均可设置可用性测试。 无需更改要测试的网站。 事实上，它甚至无需是你拥有的站点。 可以测试服务所依赖的 REST API 的可用性。

## <a name="types-of-tests"></a>测试类型

有四种类型的可用性测试：

* [URL ping 测试（经典）](monitor-web-app-availability.md)：可以通过门户创建此简单测试，以验证终结点是否正在响应，并度量与该响应关联的性能。 还可以设置自定义成功标准，以及更多高级功能，例如分析从属请求、允许重试。
* [标准测试（预览）](availability-standard-tests.md)：此单个请求测试类似于 URL ping 测试。 它包括 SSL 证书有效性、主动生存期检查、HTTP 请求谓词（例如 `GET`、`HEAD` 或 `POST`）、自定义标头以及与 HTTP 请求关联的自定义数据。
* [多步骤 Web 测试（经典）](availability-multistep.md)：可以播放这一系列 Web 请求记录来测试更复杂的场景。 多步骤 Web 测试在 Visual Studio Enterprise 中创建并上传到门户，你可以在其中运行这些测试。
* [自定义 TrackAvailability 测试](availability-azure-functions.md)：如果你决定创建自定义应用程序以运行可用性测试，可以使用 [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) 方法将结果发送到 Application Insights。

> [!IMPORTANT]
> [URL ping 测试](monitor-web-app-availability.md)和[多步骤 Web 测试](availability-multistep.md)都依赖公共 Internet 的 DNS 基础结构来解析已测试终结点的域名。 如果使用的是专用 DNS，则必须确保公共域名服务器可以删除测试的每个域名。 如果无法做到这一点，可以改为使用[自定义 TrackAvailability 测试](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)。

对于每个 Application Insights 资源，最多可以创建 100 个可用性测试。

## <a name="troubleshooting"></a>故障排除

请参阅专用[疑难解答文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [多步骤 Web 测试](availability-multistep.md)
* [URL 测试](monitor-web-app-availability.md)
* [使用 Azure Functions 创建和运行自定义可用性测试](availability-azure-functions.md)
* [Web 测试 Azure 资源管理器模板](/azure/templates/microsoft.insights/webtests?tabs=json)