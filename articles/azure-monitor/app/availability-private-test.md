---
title: 专用可用性测试 - Azure Monitor Application Insights
description: 了解如何通过专用测试在防火墙后面运行的内部服务器上使用可用性测试。
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: dca75f6497567c5c6855fc1b5b12aa17a41b3dec
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103642"
---
# <a name="private-testing"></a>专用测试

如果要在防火墙后面运行的内部服务器上使用可用性测试，有两种可能的解决方案：公共 ping 测试启用，和断开连接/无引入方案。

## <a name="public-ping-test-enablement"></a>公共 ping 测试启用

> [!NOTE]
> 如果你不希望允许任何流量进入环境，请使用[断开连接或无引入方案](#disconnected-or-no-ingress-scenarios)部分中的方法。

 确保内部网站有公共 DNS 记录。 如果无法解析 DNS，则测试将失败。 [为内部应用程序创建自定义域名。](../../cloud-services/cloud-services-custom-domain-name-portal.md#add-an-a-record-for-your-custom-domain)

配置防火墙以允许来自我们的服务的传入请求。

- [服务标记](../../virtual-network/service-tags-overview.md)是启用 Azure 服务的一种简单方法，无需授权单个 IP 或维护最新列表。 服务标记可在 Azure 防火墙和网络安全组中使用，以允许进行服务访问。 ApplicationInsightsAvailability 是专用于 ping 测试服务的服务标记。
    1. 如果使用 [Azure 网络安全组](../../virtual-network/network-security-groups-overview.md)，请转到你的网络安全组资源，在“设置”下选择“入站安全规则”，然后选择“添加”。

         :::image type="content" source="media/availability-private-test/add.png" alt-text="网络安全组资源中“入站安全规则”选项卡的屏幕截图。":::

    1. 接下来，选择“服务标记”作为源，选择“ApplicationInsightsAvailability”作为源服务标记 。 为来自服务标记的传入流量使用端口 80 (http) 和 443 (https)。

        :::image type="content" source="media/availability-private-test/service-tag.png" alt-text="包含服务标记源的“添加入站安全规则”选项卡的屏幕截图。":::

- 如果终结点托管在 Azure 外部，或者服务标记不适用于你的方案，则需要单独将 [Web 测试代理的 IP 地址](ip-addresses.md)加入允许列表。 可以直接从 PowerShell、Azure CLI 或使用[服务标记 API](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 从 REST 调用查询 IP 范围。还可以下载 [JSON 文件](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)以获取当前服务标记列表，其中包含 IP 地址详细信息。
    1. 在网络安全组资源中，选择“设置”下的“入站安全规则”，然后选择“添加”。
    1. 接下来，选择“IP 地址”作为源，然后在源 IP 地址/CIRD 范围的逗号分隔列表中添加你的 IP 地址。

         :::image type="content" source="media/availability-private-test/ip-addresses.png" alt-text="包含 IP 地址源的“添加入站安全规则”选项卡的屏幕截图。":::

## <a name="disconnected-or-no-ingress-scenarios"></a>断开连接或无引入方案

测试服务器需要以传出访问的方式访问 Application Insights 引入终结点，与允许传入请求相比，这种方式的安全风险要小得多。 结果将显示在“可用性 Web 测试”选项卡中，与通过 Azure 门户创建的测试相比，体验将会简化。 自定义可用性测试还会作为可用性结果显示在“分析”、“搜索”和“指标”中。

1. 使用 [Azure 专用链接](../logs/private-link-security.md)来连接 Application Insights 资源和已断开连接的环境
1. 编写自定义代码，以定期测试内部服务器或终结点。 可以在防火墙后面的测试服务器上使用 [Azure Functions](availability-azure-functions.md) 或后台进程来运行代码。 测试进程可以通过核心 SDK 包中的`TrackAvailability()` API 将其结果发送到 Application Insights。

## <a name="troubleshooting"></a>故障排除

专用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [Azure 专用链接](../logs/private-link-security.md)
* [可用性警报](availability-alerts.md)
* [URL 测试](monitor-web-app-availability.md)
* [使用 Azure Functions 创建和运行自定义可用性测试](availability-azure-functions.md)