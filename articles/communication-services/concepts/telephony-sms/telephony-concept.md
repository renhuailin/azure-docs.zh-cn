---
title: Azure 通信服务的 PSTN 电话服务集成概念
description: 了解如何在 Azure 通信服务应用程序中集成 PSTN 通话功能。
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d5e4920dcc422e848266f35c8a59175b5149b924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492734"
---
# <a name="telephony-concepts"></a>电话服务概念

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure 通信服务通话客户端库可用于向应用程序添加电话服务和 PSTN。 此页汇总了重要的电话服务概念和功能。 若要详细了解特定客户端库语言和功能，请参阅[通话库](../../quickstarts/voice-video-calling/calling-client-samples.md)。

## <a name="overview-of-telephony"></a>电话服务概述
每当你的用户与传统电话号码交互时，将通过 PSTN（公用电话交换网）语音呼叫来辅助完成呼叫。 若要拨打和接收 PSTN 呼叫，需要向 Azure 通信服务资源添加电话功能。 在这种情况下，信令和媒体结合使用基于 IP 和基于 PSTN 的技术来连接用户。 通信服务提供两种不同的方式来连接 PSTN 网络：Azure 云通话和 SIP 接口。

### <a name="azure-cloud-calling"></a>Azure 云通话

这是向应用或服务添加 PSTN 连接的一种简单方法，在本例中，Microsoft 是你的电信提供商。 可以直接从 Microsoft 购买号码。 Azure 云通话是用于通信服务的完全通过云实现的电话服务解决方案。 这是将 ACS 连接到公用电话交换网 (PSTN) 以呼叫座机和移动电话的最简单选项。 使用此选项后，Microsoft 将充当你的 PSTN 运营商，如下图所示：

![Azure 云通话关系图。](../media/telephony-concept/azure-calling-diagram.png)

如果你对以下问题的回答为“是”，那么 Azure 云通话就是适合你的解决方案：
- Azure 云通话在你的区域中可用。
- 你不需要保留当前的 PSTN 运营商。
- 你想对 PSTN 使用 Microsoft 管理的访问。

使用此选项：
- 你直接从 Microsoft 获得号码，且可拨打世界各地的电话。
- 你不需要部署或维护本地部署，因为 Azure 云通话在 Azure 通信服务之外运行。
- 注意：如有必要，可以选择通过 SIP 接口连接受支持的会话边界控制器 (SBC)，以与第三方 PBX、模拟设备和其他 SBC 支持的第三方电话服务设备进行互操作。

此选项要求与 Azure 通信服务的连接不间断。

### <a name="sip-interface"></a>SIP 接口

利用此选项，可将旧的本地电话服务和所选的运营商连接到 Azure 通信服务。 即使你所在国家/地区未提供 Azure 云通话，此选项也可提供对 ACS 应用程序的 PSTN 通话功能。 

![SIP 接口关系图。](../media/telephony-concept/sip-interface-diagram.png)

如果你对以下问题的回答为“是”，那么 SIP 接口就是适合你的解决方案：

- 你希望使用具有 PSTN 通话功能的 ACS。
- 你需要保留当前的 PSTN 运营商。
- 你需要混合路由，即一部分通话通过 Azure 云通话进行，一部分通过运营商进行。
- 你需要与第三方 PBX 和/或设备（如空中寻呼机、模拟设备等）进行互操作。

使用此选项：

- 你不需要额外的本地软件即可将自己受支持的 SBC 连接到 Azure 通信服务。
- 可以将任何电话服务运营商与 ACS 配合使用。
- 可以选择配置和管理此选项，也可以由运营商或合作伙伴来配置和管理（询问运营商或合作伙伴是否提供此选项）。
- 可以配置电话服务设备（例如第三方 PBX 和模拟设备）和 ACS 之间的互操作性。

此选项需要以下条件：

- 不间断连接到 Azure。
- 部署和维护受支持的 SBC。
- 与第三方运营商的合约。 （除非为使用通信服务的用户部署为选项，以提供与第三方 PBX、模拟设备或其他电话服务设备的连接。）

## <a name="next-steps"></a>后续步骤

### <a name="conceptual-documentation"></a>概念文档

- [Azure 通信服务中的电话号码类型](./plan-solution.md)
- [计划 SIP 接口](./sip-interface-infrastructure.md)
- [定价](../pricing.md)

### <a name="quickstarts"></a>快速入门

- [获取电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [拨打电话](../../quickstarts/voice-video-calling/pstn-call.md)
