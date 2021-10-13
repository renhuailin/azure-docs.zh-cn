---
title: Azure 直接路由认证的会话边界控制器 - Azure 通信服务
description: 针对 Azure 通信服务直接路由认证的会话边界控制器列表，以及已知限制
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: 2270e29cc9b279348467ca2a6f6593269e37eafb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659804"
---
# <a name="list-of-session-border-controllers-certified-for-azure-communication-services-direct-routing"></a>针对 Azure 通信服务直接路由认证的会话边界控制器列表
本文档包含经认证可用于 Azure 通信服务直接路由的会话边界控制器列表。 此外，它还包含已知限制。

Microsoft 正在与选定的会话边界控制器 (SBC) 供应商合作，以认证其 Teams 直接路由可搭配 Azure 直接路由使用。 你可以在此页面查看进度。 虽然经认证可用于 Teams 直接路由的 SBC 可以搭配 Azure 直接路由使用，但我们不鼓励在 SBC 显示在此页面之前在其上放置任何工作负载。 我们也不支持未认证的 SBC。 虽然 Azure 直接路由与 Teams 直接路由在同一后端上构建，但存在一些差异。 该认证涵盖针对 Azure 直接路由的 SBC 全面验证。

Microsoft 与每个供应商合作，以便：
- 共同处理 SIP 互连协议。
- 使用第三方实验室执行严格测试。 只有通过测试的设备才会得到认证。
- 在生产环境和预生产环境中使用所有已认证设备运行每日测试。 在预生产环境中验证设备可以保证云中的新版 Azure 通信服务代码可用于经过认证的 SBC。
- 与 SBC 供应商建立联合支持流程。

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Azure 通信服务尚不支持媒体旁路。 基于 Web 的客户端不支持早期媒体。
下表列出了经认证可用于 Azure 通信服务直接路由的设备。

如果你对通信服务直接路由的 SBC 认证计划有任何疑问，请联系 acsdrcertification@microsoft.com。

## <a name="certified-sbc-vendors"></a>经过认证的 SBC 供应商

|Vendor|产品|软件版本|
|:--- |:--- |:--- 
|[AudioCodes](https://www.audiocodes.com/media/lbjfezwn/mediant-sbc-with-microsoft-azure-communication-services.pdf)|Mediant SBC|7.40A
|[Metaswitch](https://manuals.metaswitch.com/Perimeta/V4.9/AzureCommunicationServicesIntegrationGuide/Source/notices.html)|Perimeta SBC|4.9|
|[Oracle](https://www.oracle.com/technical-resources/documentation/acme-packet.html)|Oracle Acme 数据包 SBC|8.4|
|功能区通信|[SBC SWe / SBC 5400 / SBC 7000](https://support.sonus.net/display/ALLDOC/Ribbon+Configurations+with+Azure+Communication+Services+Direct+Routing)|9.02|
||[SBC SWe Lite/SBC 1000/SBC 2000](https://support.sonus.net/display/UXDOC90/Best+Practice+-+Configure+SBC+Edge+for+Azure+Communication+Services+Direct+Routing)|9.0

请注意授予主版本的认证。 这意味着支持 SBC 固件中该主版本后面具有任意数字的固件。

## <a name="next-steps"></a>后续步骤

### <a name="conceptual-documentation"></a>概念文档

- [Azure 通信服务中的电话号码类型](./plan-solution.md)
- [规划 Azure 直接路由](./direct-routing-infrastructure.md)
- [配对会话边界控制器并配置语音路由](./direct-routing-provisioning.md)
- [定价](../pricing.md)

### <a name="quickstarts"></a>快速入门

- [拨打电话](../../quickstarts/voice-video-calling/pstn-call.md)
