---
title: Azure 直接路由预配和配置 - Azure 通信服务
description: 了解如何为 Azure 通信服务直接路由添加会话边界控制器和配置语音路由
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: b6fa8523a347f9191c607ce3ba50b32f2d088886
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636014"
---
# <a name="session-border-controllers-and-voice-routing"></a>会话边界控制器和语音路由
使用 Azure 通信服务直接路由可将现有电话服务基础结构连接到 Azure。 本文列出了将支持的会话边界控制器 (SBC) 连接到直接路由所要执行的概要步骤，并介绍了如何对已启用的通信资源运行语音路由。 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]
 
有关 Azure 通信服务直接路由是否是适合你组织的解决方案的信息，请参阅 [Azure 电话服务概念](./telephony-concept.md)。 有关先决条件和部署规划的信息，请参阅[通信服务直接路由基础结构要求](./direct-routing-infrastructure.md)。

## <a name="connect-the-sbc-with-azure-communication-services"></a>将 SBC 连接到 Azure 通信服务

### <a name="configure-using-azure-portal"></a>使用 Azure 门户进行配置 
1. 在左侧导航栏中，选择“语音呼叫 - PSTN”下的“直接路由”，然后从“会话边界控制器”选项卡中选择“配置”。
1. 输入 SBC 的完全限定域名和信号端口。
 
如果使用的是 Office 365，请确保 SBC FQDN 的域部分不同于在 Office 365 管理门户中的“域”下注册的域。 
- 例如，如果在 O365 中注册的域是 `contoso.com`，则不能对通信服务使用 `sbc.contoso.com`。 但是，如果 O365 中不存在上级域，可以使用上级域：可以创建 `acs.contoso.com` 域并使用 FQDN `sbc.acs.contoso.com` 作为 SBC 名称。
- SBC 证书必须与该名称匹配；通配符证书受支持。
- *.onmicrosoft.com 域不可用于 SBC 的 FQDN。
有关完整的要求列表，请参阅 [Azure 直接路由基础结构要求](./direct-routing-infrastructure.md)。

   :::image type="content" source="../media/direct-routing-provisioning/add-session-border-controller.png" alt-text="添加会话边界控制器。":::
- 完成后，单击“下一步”。
如果所有设置正确，你应会看到 Microsoft 与会话边界控制器之间的 OPTIONS 消息交换，此时可以使用 SBC 监视/日志来验证连接。

## <a name="voice-routing-considerations"></a>语音路由注意事项

Azure 通信服务直接路由采用一种允许根据被呼叫号码模式将呼叫发送到特定会话边界控制器 (SBC) 的路由机制。
将直接路由配置添加到某个资源时，从此资源的实例（标识）发起的所有呼叫首先会尝试接入直接路由主干线。 路由基于拨打的号码，以及为资源配置的语音路由中的匹配项。 如果存在匹配项，则呼叫将会通过直接路由主干线。 如果没有匹配项，则下一步是处理 callAgent.startCall 方法的 alternateCallerId 参数。 如果该资源已启用语音呼叫 (PSTN) 并至少具有一个从 Microsoft 购买的号码，并且 alternateCallerId 与该资源的某个已购买号码匹配，那么，将使用 Microsoft 基础结构通过语音呼叫 (PSTN) 来路由呼叫。 如果 alternateCallerId 参数与购买的任何号码都不匹配，则呼叫将会失败。 下图演示了 Azure 通信服务语音路由逻辑。

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-diagram.png" alt-text="通信服务传出语音路由。":::

## <a name="voice-routing-examples"></a>语音路由示例
以下示例显示了呼叫流中的语音路由。

> [!NOTE]
> 在所有示例中，尽管较高语音路由的优先级较高，但会按随机顺序尝试路由中的 SBC。

### <a name="one-route-example"></a>一个路由的示例：
如果你创建了一个模式为 `^\+1(425|206)(\d{7})$` 的语音路由并在其中添加了 `sbc1.contoso.biz` 和 `sbc2.contoso.biz`，那么，当用户呼叫 `+1 425 XXX XX XX` 或 `+1 206 XXX XX XX` 时，呼叫将首先路由到 SBC `sbc1.contoso.biz` 或 `sbc2.contoso.biz`。 如果这两个 SBC 都不可用，则会中断该呼叫。

### <a name="two-routes-example"></a>两个路由的示例：
如果你创建了一个模式为 `^\+1(425|206)(\d{7})$` 的语音路由并在其中添加了 `sbc1.contoso.biz` 和 `sbc2.contoso.biz`，然后创建了模式相同的另一个路由并在其中添加了 `sbc3.contoso.biz` 和 `sbc4.contoso.biz`。 在这种情况下，当用户呼叫 `+1 425 XXX XX XX` 或 `+1 206 XXX XX XX` 时，呼叫将首先路由到 SBC `sbc1.contoso.biz` 或 `sbc2.contoso.biz`。 如果 sbc1 和 sbc2 都不可用，则尝试使用优先级更低的路由（`sbc3.contoso.biz` 和 `sbc4.contoso.biz`）。 如果第二个路由的 SBC 都不可用，则会中断该呼叫。

### <a name="three-routes-example"></a>三个路由的示例：
如果你创建了一个模式为 `^\+1(425|206)(\d{7})$` 的语音路由并在其中添加了 `sbc1.contoso.biz` 和 `sbc2.contoso.biz`，然后创建了模式相同的另一个路由并在其中添加了 `sbc3.contoso.biz` 和 `sbc4.contoso.biz`，并且还创建了模式为 `^+1(\d[10])$` 的第三个路由并在其中添加了 `sbc5.contoso.biz`。 在这种情况下，当用户呼叫 `+1 425 XXX XX XX` 或 `+1 206 XXX XX XX` 时，呼叫将首先路由到 SBC `sbc1.contoso.biz` 或 `sbc2.contoso.biz`。 如果 sbc1 和 sbc2 都不可用，则尝试使用优先级更低的路由（`sbc3.contoso.biz` 和 `sbc4.contoso.biz`）。 如果第二个路由的 SBC 都不可用，则尝试使用第三个路由；如果 sbc5 也不可用，则会中断该呼叫。 此外，如果用户拨打 `+1 321 XXX XX XX`，则呼叫将转到 `sbc5.contoso.biz`，如果该 SBC 不可用，则会中断该呼叫。

> [!NOTE]
> 故障转移到语音路由中的下一个 SBC 仅适用于响应代码 408、503 和 504。

> [!NOTE]
> 在所有示例中，如果拨打的号码与模式不匹配，则除非通信资源具有已购买的号码，并且此号码在应用程序中用作 `alternateCallerId`，否则会中断该呼叫。 

## <a name="configure-voice-routing"></a>配置语音路由 

### <a name="configure-using-azure-portal"></a>使用 Azure 门户进行配置

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-configuration.png" alt-text="通信服务传出语音路由配置。":::

为语音路由指定名称，使用正则表达式指定号码模式，然后为该模式选择 SBC。 下面是基本正则表达式的一些示例：
- `^\+\d+$` - 匹配包含一个或多个数字且以加号开头的电话号码
- `^+1(\d[10])$` - 匹配 `+1` 后面有 10 个数字的电话号码
- `^\+1(425|206)(\d{7})$` - 匹配以 `+1425` 或 `+1206` 开头并后接 7 个数字的电话号码
- `^\+0?1234$` - 匹配 `+01234` 和 `+1234` 电话号码。

有关正则表达式的详细信息，请参阅 [.NET 正则表达式概述](/dotnet/standard/base-types/regular-expressions)。

可为单个模式选择多个 SBC。 在这种情况下，路由算法将按随机顺序选择 SBC。 还可以多次指定确切的号码模式。 较高行的优先级较高，如果与该行关联的所有 SBC 都不可用，则会选择下一行。 这样便可以创建复杂的路由方案。

## <a name="delete-direct-routing-configuration"></a>删除直接路由配置

### <a name="delete-using-azure-portal"></a>使用 Azure 门户删除

#### <a name="to-delete-a-voice-route"></a>若要删除语音路由，请执行以下操作：
1. 在左侧导航栏中，转到“语音呼叫 - PSTN”下的“直接路由”，然后选择“语音路由”选项卡。
1. 使用复选框选择要删除的一个或多个路由。
1. 选择“删除”。

#### <a name="to-delete-an-sbc"></a>若要删除 SBC，请执行以下操作：
1. 在左侧导航栏中，转到“语音呼叫 - PSTN”下的“直接路由”。
1. 在“会话边界控制器”选项卡上，选择“配置”。
1. 清除要删除的 SBC 的 FQDN 和端口字段，然后选择“下一步”。
1. 在“语音路由”选项卡上查看语音路由配置，并根据需要进行更改。 选择“保存”。

> [!NOTE]
> 删除与某个语音路由关联的 SBC 时，可以在“语音路由”选项卡上为该路由选择其他 SBC。将删除没有 SBC 的语音路由。

## <a name="next-steps"></a>后续步骤

### <a name="conceptual-documentation"></a>概念文档

- [为 Azure 通信服务直接路由认证的会话边界控制器](./certified-session-border-controllers.md)
- [定价](../pricing.md)

### <a name="quickstarts"></a>快速入门

- [拨打电话](../../quickstarts/voice-video-calling/pstn-call.md)
