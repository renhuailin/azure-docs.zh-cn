---
title: Application Insights 可用性概述
description: 设置定期 Web 测试，以监视应用或网站的可用性和响应能力。
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600788"
---
# <a name="availability-tests-overview"></a>可用性测试概述

部署 Web 应用/网站后，可以设置重复执行的测试来监视可用性和响应能力。 [Application Insights](./app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果应用程序无响应或响应太慢，它可以提醒你。

对于可以从公共 Internet 访问的任何 HTTP 或 HTTPS 终结点，均可设置可用性测试。 无需更改要测试的网站。 事实上，该网站甚至不需要是你拥有的网站。 可以测试服务所依赖的 REST API 的可用性。

## <a name="types-of-availability-tests"></a>可用性测试类型

有三种类型的可用性测试：

* [URL ping 测试](monitor-web-app-availability.md)：此类别有两个可通过门户创建的简单测试。
* [多步骤 Web 测试](availability-multistep.md)：记录一系列 Web 请求，这些请求可以通过再现来测试更复杂的场景。 多步骤 Web 测试在 Visual Studio Enterprise 中创建并上传到门户执行。
* [自定义跟踪可用性测试](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)：如果决定创建自定义应用程序以运行可用性测试，则可以使用 `TrackAvailability()` 方法将结果发送到 Application Insights。

> [!IMPORTANT]
> [URL ping 测试](monitor-web-app-availability.md)和[多步骤 Web 测试](availability-multistep.md)都依赖公共 Internet DNS 基础结构来解析已测试终结点的域名。 这意味着，如果你使用专用 DNS，则必须确保测试的每个域名也可由公共域名服务器解析，如果不可能，则可以改用[自定义跟踪可用性测试](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)。

**对于每个 Application Insights 资源，最多可以创建 100 个可用性测试。**

## <a name="troubleshooting"></a>疑难解答

专用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-step"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [多步骤 Web 测试](availability-multistep.md)
* [URL 测试](monitor-web-app-availability.md)
* [使用 Azure Functions 创建和运行自定义可用性测试。](availability-azure-functions.md)