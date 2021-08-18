---
title: Azure 服务总线中 AMQP 1.0 的概述
description: 了解 Azure 服务总线如何支持高级消息队列协议（AMQP，一种开放标准协议）。
ms.topic: article
ms.date: 04/08/2021
ms.openlocfilehash: 0e976f0cba4a599b64fde57f3a271a1565f93417
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416895"
---
# <a name="advanced-message-queueing-protocol-amqp-10-support-in-service-bus"></a>服务总线中的高级消息队列协议 (AMQP) 1.0 支持
Azure 服务总线云服务使用 [AMQP 1.0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) 作为其主要通信方式。 在过去的十年中，Microsoft 与行业内的合作伙伴（相互竞争的消息传递代理的客户和供应商）开展合作，开发和发展 AMQP，并在 [OASIS AMQP 技术委员会](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)中开发了新的扩展。 AMQP 1.0 是 ISO 和 IEC 标准 ([ISO 19464:20149](https://www.iso.org/standard/64955.html))。 

通过 AMQP 可使用独立于供应商和实现的开放标准协议构建跨平台的混合应用程序。 可以借助使用不同语言和框架构建的且运行在不同操作系统上的组件来构建应用程序。 所有这些组件均可连接到服务总线，并且能够高效且完全无损地无缝交换结构化业务消息。

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>简介：什么是 AMQP 1.0，为何它很重要？
通常，面向消息的中间件产品始终使用专用协议来支持客户端应用程序和代理之间的通信。 这意味着，在选择特定供应商的消息传递代理后，必须使用该供应商的库将客户端应用程序连接到该代理。 这会导致在一定程度上依赖于该供应商，因为将应用程序移植到其他产品需要对所有已连接应用程序进行代码改写。 在 Java 社区中，特定于语言的 API 标准（如 Java 消息服务 (JMS)）和 Spring Framework 的抽象在某种程度上缓和了这一痛点，但其功能范围非常窄，并且会将使用其他语言的开发人员排除在外。

此外，连接来自不同供应商的消息传递代理比较棘手。 这通常需要通过应用程序级桥接来将消息从一个系统移到另一个系统，并在其专用消息格式之间进行转换。 这是一个常见的要求；例如，在必须向较旧的独立系统提供新的统一接口时，或者在合并后集成 IT 系统时，会有这样的要求。 AMQP 允许与连接代理直接互连，例如使用 [Apache Qpid Dispatch Router](https://qpid.apache.org/components/dispatch-router/index.html) 之类的路由器或 [RabbitMQ](service-bus-integrate-with-rabbitmq.md) 之类的代理本机“铲子”。

软件产业是一个飞速发展的产业；新编程语言和应用程序框架的引入速度有时会非常惊人。 同样，IT 系统的要求随着时间不断变化，并且开发人员希望利用最新的平台功能。 但有时候，所选消息传送供应商不支持这些平台。 如果使用的是专用消息协议，其他供应商无法为这些新平台提供库。 因此，必须使用构建网关等手段，或者可让你继续使用消息传递产品的网桥。

这些问题推动了高级消息队列协议 (AMQP) 1.0 的开发。 这种协议源于 JP Morgan Chase，像多数金融服务公司一样，该公司大量使用面向消息的中间件。 目标非常简单：就是创建一个开放标准消息传送协议，从而能够借助使用不同语言、框架和操作系统构建的组件来构建基于消息的应用程序，而所有这些应用程序都使用各个供应商提供的同类最佳组件。

## <a name="amqp-10-technical-features"></a>AMQP 1.0 技术特性
AMQP 1.0 是一个高效、可靠的线级消息传递协议，可用于构建强大、跨平台的消息传递应用程序。 协议有一个简单的目标：定义用于在两方之间安全、可靠且高效传输消息的机制。 这些消息本身使用可移植数据表示进行编码，这种表示支持不同发送者和接收者完全无损地交换结构化业务消息。 下面简要介绍几个最重要的特性：

* **高效**：AMQP 1.0 是一个面向连接的协议，它将二进制编码用于协议指令以及通过该协议传输的业务消息。 它融合了复杂的流控制方案，可最大限度地利用网络和已连接组件。 即便如此，但该协议的设计目的是实现有效性、灵活性和互操作性之间的平衡。
* 可靠：使用 AMQP 1.0 协议交换消息时，可获得一系列可靠性保证，如即发即弃 (fire-and-forget) 和可靠的恰一次确认传送 (exactly-once acknowledged delivery)。
* **灵活**：AMQP 1.0 是一个灵活的协议，可用于支持不同的拓扑。 可以将同一协议用于客户端到客户端、客户端到代理以及代理到代理通信。
* 独立于代理模型：AMQP 1.0 规范对代理所使用的消息传送模型不作任何要求。 这意味着可以向现有消息传送代理中轻松添加 AMQP 1.0 支持。

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 是一种标准（带有大写字母“S”）
AMQP 1.0 是一种国际标准，已通过 ISO 和 IEC 的认证，认证编号为 ISO/IEC 19464:2014。

自 2008 年以来，AMQP 1.0 一直由一个由 20 多家公司（包括技术提供商和最终用户企业）组成的核心小组进行开发。 在此期间，用户企业提供其实际业务需求，技术供应商则开发该协议来满足这些需求。 在整个过程中，供应商参加了研讨会，并在会上协作验证了其实现之间的互操作性。

2011 年 10 月，开发工作提交给结构化信息标准促进组织（Organization for the Advancement of Structured Information Standards，OASIS）内的技术委员会，随后 OASIS AMQP 1.0 标准于 2012 年 10 月发布。 在开发该标准期间，以下公司参与了技术委员会的工作：

* **技术供应商**：Axway Software、Huawei Technologies、IIT Software、INETCO Systems、Kaazing、Microsoft、Mitre Corporation、Primeton Technologies、Progress Software、Red Hat、SITA、Software AG、Solace Systems、VMware、WSO2、Zenika。
* **企业用户**：Bank of America、Credit Suisse、Deutsche Boerse、Goldman Sachs、JPMorgan Chase。

[OASIS AMQP 技术委员会](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)的现任主席代表 Red Hat 和 Microsoft。

开放标准的公认好处包括：

* 供应商锁定的几率较低
* 互操作性
* 有大量库和工具可供使用
* 不会过时
* 随时可找到知识渊博的工作人员
* 风险较低且可控

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 和 Service Bus
Azure 服务总线中对 AMQP 1.0 的支持意味着，你可以通过一系列使用有效二进制协议的平台来使用服务总线队列，以及发布/订阅中转消息传送功能。 此外，还可以生成由结合使用多个语言、框架和操作系统构建的组件组成的应用程序。

下图显示了一个部署示例，其中 Java 客户端运行在 Linux 上，并使用标准 Java 消息服务 (JMS) API 写入数据；而 .NET 客户端运行在 Windows 上，并通过服务总线使用 AMQP 1.0 交换消息。

![此图显示一个服务总线交换消息，其中包含两个 Linux 环境和两个 Windows 环境。][0]

**图 1：演示使用服务总线和 AMQP 1.0 进行跨平台消息传送的部署方案示例**

通过 Azure SDK 提供的所有受支持的服务总线客户端库均使用 AMQP 1.0。

- [适用于 .NET 的 Azure 服务总线](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [适用于 Java 的 Azure 服务总线库](/java/api/overview/azure/servicebus?preserve-view=true)
- [适用于 Java JMS 2.0 的 Azure 服务总线提供程序](how-to-use-java-message-service-20.md)
- [适用于 JavaScript 和 TypeScript 的 Azure 服务总线模块](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [适用于 Python 的 Azure 服务总线库](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](./includes/service-bus-websockets-options.md)]

此外，还可以使用任何 AMQP 1.0 兼容协议堆栈中的服务总线：

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>总结
* AMQP 1.0 是一个开放、可靠的消息传递协议，可用于构建跨平台的混合应用程序。 AMQP 1.0 是一种 OASIS 标准。

## <a name="next-steps"></a>后续步骤
准备好了解详细信息？ 请访问以下链接：

* [使用 AMQP 通过 .NET 使用服务总线]
* [使用 AMQP 通过 Java 使用服务总线]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[使用 AMQP 通过 .NET 使用服务总线]: service-bus-amqp-dotnet.md
[使用 AMQP 通过 Java 使用服务总线]: ./service-bus-java-how-to-use-jms-api-amqp.md

