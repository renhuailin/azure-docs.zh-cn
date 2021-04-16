---
title: Azure 通信服务 UI 框架概述
titleSuffix: An Azure Communication Services conceptual document
description: 了解 Azure 通信服务 UI 框架
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4ab1a157cdf3ef5017b227cd090379dcab91997e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105931550"
---
# <a name="azure-communication-services-ui-framework"></a>Azure 通信服务 UI 框架

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="预生成和自定义复合组件的比较":::

Azure 通信服务 UI 框架使你能够轻松地构建现代通信用户体验。 它提供了一个可用于生产的 UI 组件库，可以将其置于应用程序中：

- 复合组件 - 这些组件是实现常见通信方案的统包解决方案。 可以向应用程序快速添加视频通话或聊天体验。 复合组件是使用基本组件生成的开源组件。
- 基本组件 - 这些组件是开源构建基块，可用于生成自定义通信体验。 提供了实现通话和聊天功能的组件，可以将这些组件组合在一起来构建体验。 

这些 UI SDK 都使用 [Microsoft 的 Fluent 设计语言](https://developer.microsoft.com/fluentui/)和资产。 Fluent UI 为 UI 框架提供了跨 Microsoft 产品进行了大量测试的基础层。

## <a name="differentiating-components-and-composites"></a>**区分组件和复合组件**

基本组件在核心 Azure 通信服务 SDK 的基础上构建，并实现基本操作，例如初始化核心 SDK、呈现视频，以及提供静音、视频开启/关闭等用户控制。你可以使用这些基本组件，通过预先构建的、可用于生产的通信组件构建你自己的自定义布局体验 。

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="UI 框架组件概述":::

复合组件将多个基本组件组合在一起，以创建更完整的通信体验。 可以轻松地将这些更高级的组件集成到现有应用中，从而创建完善的通信体验，而无需从头开始构建。 开发人员可以集中精力在应用中构建所需的周边体验和流程，而将通信复杂性留给复合组件。

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="UI 框架复合组件概述":::

## <a name="what-ui-framework-is-best-for-my-project"></a>哪些 UI 框架最适合我的项目？

了解这些要求有助于你选择正确的 SDK：

- **你需要多少自定义？** Azure 通信核心 SDK 没有用户体验，它的设计初衷是让你可以构建你想要的任何用户体验。 UI 框架组件以减少自定义为代价提供 UI 资产。
- **是否需要会议功能？** 会议系统有几种独特的功能，这些功能当前在核心 Azure 通信服务 SDK 中不可用，例如模糊背景和举手。
- **你的目标平台是什么？** 不同平台具有不同功能。

有关可变 UI SDK 中的功能可用性的详细信息，请参阅[此处](ui-sdk-features.md)，但下面汇总了一些重要权衡。

|SDK/SDK|实施复杂性|   自定义功能|  调用| 聊天| [团队互操作](./../teams-interop.md)
|---|---|---|---|---|---|---|
|复合组件|低|低|✔|✔|✕
|基本组件|中|中|✔|✔|✕
|核心 SDK|高|高|✔|✔ |✔

## <a name="cost"></a>成本

使用 Azure UI 框架不会有任何额外的 Azure 成本或计量。 只需使用相同的通话、聊天和 PSTN 计量为使用的基础服务付费。

## <a name="supported-use-cases"></a>支持用例

通话：

- 使用组 ID 加入 Azure 通信服务通话

聊天：

- 使用线程 ID 加入 Azure 通信服务聊天

## <a name="supported-identities"></a>支持的标识：

需要使用 Azure 通信服务标识来初始化 UI 框架并对该服务进行身份验证。 有关身份验证的详细信息，请参阅[身份验证](../authentication.md)和[访问令牌](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>建议体系结构 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="UI 框架推荐的体系结构和客户端服务器体系结构 ":::

复合组件和基本组件使用 Azure 通信服务访问令牌进行初始化。 应通过你所管理的受信任服务从 Azure 通信服务获得访问令牌。 请参阅[快速入门：创建访问令牌](../../quickstarts/access-tokens.md)和[受信任的服务教程](../../tutorials/trusted-service-tutorial.md)来获取详细信息。

这些 SDK 还需要它们将要加入的通话或聊天的上下文。 与用户访问令牌类似，此上下文应通过你自己的受信任服务传送到客户端。 以下列表汇总了需要操作的初始化和资源管理功能。

| Contoso 责任                                 | UI 框架责任                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| 从 Azure 提供访问令牌                    | 传递给定的访问令牌来初始化组件        |
| 提供刷新功能                                 | 使用开发人员提供的功能来刷新访问令牌          |
| 检索/传递用于通话或聊天的联接信息          | 传递通话和聊天信息以初始化组件 |
| 检索/传递任何自定义数据模型的用户信息 | 将自定义数据模型传递到要呈现的组件          |
