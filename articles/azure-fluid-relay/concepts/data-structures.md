---
title: 分布式数据结构
description: 分布式数据结构是 Fluid 应用程序的构建基块
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/data-structures/overview/
ms.openlocfilehash: c79fea26de7cda6d3097c2a8c9403420451fe665
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661543"
---
# <a name="distributed-data-structures"></a>分布式数据结构

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

Fluid Framework 为开发人员提供分布式数据结构 (DDS)，以自动确保每个连接的客户端都可以访问相同的状态。 以前使用过通用数据结构的程序员都应该很熟悉 DDS 提供的 API。

> [!NOTE]
> 本文假设你已阅读 fluidframework.com 上的 [Introducing Distributed Data Structures](https://fluidframework.com/docs/build/dds/)（分布式数据结构简介）并熟悉这种结构。

分布式数据结构的行为类似于本地数据结构。 代码可以在此结构中添加数据、删除数据、更新数据等。但是，DDS 并非本地对象。 某个 DDS 也可以由公开该 DDS 的同一父容器的其他客户端更改。 由于多个用户可以同时更改同一个 DDS，因此你需要考虑使用哪个 DDS 来为数据建模。

> [!NOTE]
> “同时”的含义
>
> 如果两个或更多客户端在从服务器收到其他客户端的更改之前各自进行了某项更改，则这种情况就称为同时更改。

为方案选择正确的数据结构可以改善应用程序的性能和代码结构。

DDS 之间的差别体现在三个特征上：

- 基本数据结构：例如键值对、序列或队列。
- 客户端自治性：乐观 DDS 使任一客户端能够单方面更改某个值，新值将中继到所有其他客户端。 但是，共识 DDS 只有在某项更改已通过共识过程由其他客户端接受时才允许该项更改。
- 合并策略：用于确定如何解决客户端进行的有冲突更改的策略。

下面列举了数据结构并描述了它们在哪种情况下可发挥最大作用。

## <a name="key-value-data"></a>键值数据

这些 DDS 用于存储键值数据。 它们属于乐观 DDS，使用“最后写入者胜出”合并策略。 虽然一个对的值可以是复杂对象，但任何给定对的值都不可直接编辑；必须将整个值替换为包含所需编辑内容的新值，并且需要整体替换。

- SharedMap：基本的键值数据结构。

### <a name="key-value-scenarios"></a>键值方案

键值数据结构是许多方案的最常见选择。

- 用户偏好数据。
- 调查表的当前状态。
- 视图的配置。

### <a name="common-issues-and-best-practices-for-key-value-ddses"></a>键值 DDS 的常见问题和最佳做法

- 在 SharedMap 中存储一个计数器会导致意外行为。 请改用 SharedCounter。
- 在键值条目中存储数组、列表或日志可能会导致意外行为，因为用户无法以协作的方式修改一个条目的不同组成部分。 请尝试将数组或列表数据存储在 SharedSequence 或 SharedInk 中。
- 在一个键值条目中存储大量数据可能导致出现性能或合并问题。 每项更新都会更新整个值，而不是合并两项更新。 请尝试将数据拆分到多个键。

## <a name="sequences"></a>序列

这些 DDS 用于存储有序数据。 它们属于乐观 DDS。 当你需要在列表或数组中的指定索引处添加或删除数据时，序列数据结构非常有用。 与键值数据结构不同，序列是有序的，可以处理多个用户同时进行的插入。

- SharedNumberSequence：数字序列。
- SharedObjectSequence：普通对象序列。

### <a name="sequence-scenarios"></a>序列方案

- 列表
- 时间线

### <a name="common-issues-and-best-practices-for-sequence-ddses"></a>序列 DDS 的常见问题和最佳做法

- 仅将不可变数据作为项存储在序列中。 更改项值的唯一方法是先将其从序列中删除，然后在旧值所在的位置插入一个新值。 但由于其他客户端可以插入和删除值，没有可靠的方法可将新值放入所需位置。

## <a name="strings"></a>字符串

SharedString DDS 用于能够以协作方式编辑的非结构化文本数据。 它属于乐观 DDS。

- `SharedString` -- 用于处理协作文本的数据结构。

### <a name="string-scenarios"></a>字符串方案

- 文本编辑器

## <a name="see-also"></a>请参阅

若要详细了解 DDS 及其用法，请参阅 fluidframework.com 的以下部分：

- [Introducing distributed data structures](https://fluidframework.com/docs/build/dds/)（分布式数据结构简介）。
- [Types of distributed data structures](https://fluidframework.com/docs/data-structures/overview/)（分布式数据结构的类型）。
