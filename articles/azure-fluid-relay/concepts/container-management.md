---
title: 管理 Fluid 容器
description: 概述如何管理 Azure Fluid Relay 服务中的容器。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 934122f4cb952a4be915c55c2555e336705a71c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710389"
---
# <a name="managing-fluid-containers"></a>管理 Fluid 容器

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

容器是 Azure Fluid Relay 服务中的存储的原子单元，表示从 Fluid 会话（包括操作和快照）存储的数据。 当用户第一次加入或再次加入时，Fluid 运行时使用容器解除冻结 Fluid 会话的状态。

使用 Fluid Framework 生成应用程序时，需要考虑几个有关容器创建和管理的事项，如此图中所述。

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="Fluid 服务的体系结构以及开发人员和 Microsoft 拥有的部分的示意图。":::

## <a name="key-concepts"></a>关键概念

### <a name="container-permissions"></a>容器权限 

在大多数情况下，开发人员需要管理容器和容器权限的清单。 这包括有关有权访问容器的用户的信息以及元数据（例如容器的易记名称）。

### <a name="accessing-containers"></a>访问容器

容器由容器 ID 引用。 用户在可以创建或打开容器之前，必须请求 Fluid 运行时在与 Azure Fluid Relay 服务通信时将使用的 JWT。 具有有效 JWT 的任何进程都可以访问容器。 开发人员负责为容器访问生成 JWT，使其能够控制业务逻辑，以根据自己的方案控制访问权限。 Azure Fluid Relay 服务不知道哪些用户应该有权访问容器。 有关此主题的详细信息，请参阅 [Azure Fluid Relay 令牌协定](../how-tos/fluid-json-web-token.md)

> [!NOTE]
> JWT 字段 documentID 对应于 Fluid 容器 ID。

### <a name="container-naming"></a>容器命名

创建容器时，由 Azure Fluid Relay 服务为容器命名。 “创建”操作返回 GUID 形式的容器名，该名称必须在以后用于打开容器。 在大多数情况下，开发人员需要将此容器 ID GUID 和易记名称一起存储在自己的数据存储中，以促进容器发现流程。 

### <a name="container-discovery"></a>容器发现

开发人员负责任何与现有容器的用户发现相关的体验和业务逻辑。 这可以采用基于用户参与 Fluid 会话的容器的可浏览列表形式，直接共享用户之间的容器，或以编程方式向现有的项目或进程分配容器。

## <a name="see-also"></a>请参阅

- [Azure Fluid Relay 体系结构概述](architecture.md)
- [分布式数据结构](data-structures.md)
- [Azure Fluid Relay 令牌协定](../how-tos/fluid-json-web-token.md)
