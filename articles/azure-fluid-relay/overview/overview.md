---
title: Azure Fluid Relay 概述
description: Fluid Framework 和 Azure Fluid Relay 的概述。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 08/19/2021
ms.topic: how-to
ms.service: azure-fluid
ms.openlocfilehash: 30c0e289951555a5ffcd1d0776e0b273f8573da7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661569"
---
# <a name="azure-fluid-relay-overview"></a>Azure Fluid Relay 概述

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

[Fluid Framework](https://fluidframework.com/) 是一种独立于平台的开源框架。 [Azure Fluid Relay](../overview/overview.md) 是 Fl​​uid Framework 的托管产品/服务，可帮助开发人员构建实时协作体验并在连接的 JavaScript 客户端之间实时复制状态。

## <a name="what-is-the-fluid-framework"></a>什么是 Fluid Framework？

Fluid Framework 是一组用于分发和同步共享状态的客户端库。 这些库允许多个客户端使用编码模式（与用于处理本地数据的编码模式类似）同时创建和操作共享数据结构。

有关更多文档，请参阅 [FluidFramework.com](https://fluidframework.com)。

## <a name="why-fluid"></a>为何使用 Fluid？

因为构建低延迟的协作体验困难重重！

Fluid Framework 提供：

- 具有数据持久性并以客户端为中心的应用程序模型，不需要自定义服务器代码。
- 采用熟悉的编程模式的分布式数据结构。
- 非常低的延迟。

Microsoft 开发人员已将协作内置于许多应用程序中，但许多应用程序需要使用特定于应用程序的服务器端逻辑来管理协作体验。 为了降低创建协作应用程序的复杂性，Microsoft 投入大量资金，于是就有了 Fluid Framework。

若是你完全不需要在服务器代码上投资，你觉得如何？ 想象一下，你可以使用一台采用轻量级和低成本设计的常规用途服务器。 你可以把开发工作的重心放在客户端体验上，由系统为你处理数据同步。 这一切，Fluid 都可以为你实现。

## <a name="focused-on-the-client-developer"></a>面向客户端开发人员

使用 Fluid Framework 生成的应用程序在服务器上不需要任何自定义代码即可启用复杂的数据同步方案，例如跨文本编辑器的实时键入。 客户端开发人员可以专注于客户体验，让 Fluid 来完成保持数据同步的工作。

Fluid Framework 可与你选择的应用程序框架配合使用。 无论你更喜欢纯 JavaScript 还是 React、Angular 或 Vue 等框架，Fluid Framework 都能让构建协作体验的过程变得简单灵活。

## <a name="how-fluid-works"></a>Fluid 的工作原理

Fluid 旨在提供性能卓越的协作体验。 为了实现此目标，团队尽可能让服务器逻辑保持简单、轻量。 这种方法有助于确保以较低的服务器成本在客户端之间进行几乎即时的同步。

为了保持服务器的简单性，每个 Fluid 客户端都负责自己的状态。 以前的系统会在服务器上保留真实来源，而 Fluid 服务负责接收数据操作，对操作进行排序，并将排序后的操作返回给客户端。 无论接收操作的顺序如何，每个客户端都能够使用该序列独立准确地生成当前状态。

以下步骤是一个典型的流程。

1. 客户端代码在本地更改数据。
1. Fluid 运行时将该更改发送到 Fluid 服务。
1. Fluid 服务对该操作进行排序并将其广播给所有客户端。
1. Fluid 运行时将该操作合并到本地数据中并引发“valueChanged”事件。
1. 客户端代码处理该事件（更新视图、运行业务逻辑）。

## <a name="getting-to-version-10"></a>迈向 1.0 版

为 Fluid Framework 提供支持的核心技术非常成熟稳定。 但是，在该基础之上构建各个层的工作仍在进行中。 未来几个月，我们将不断发展 API，增添新功能，争取进一步简化该框架的使用。 这些改变的驱动力源自 Microsoft 内部对 Fluid 的使用以及从目前在 Fluid 上开展构建工作的开发人员那里收集的各种需求。

Fluid Framework 尚未准备好为生产级解决方案提供支持。 但是，我们很高兴能在这个时候开放源代码，让开发人员有机会通过反馈和直接参与来探索、学习以及贡献自己的一份力量。
