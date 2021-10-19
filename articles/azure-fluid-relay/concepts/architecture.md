---
title: Azure Fluid Relay 体系结构概述
description: Azure Fluid Relay 体系结构概述
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/overview/
ms.openlocfilehash: 6b58c90692a7d4eba404e4d5da84b175d229f343
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661520"
---
# <a name="overview-of-azure-fluid-relay-architecture"></a>Azure Fluid Relay 体系结构概述

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

使用 Fluid 生成应用程序时需要了解三个主要概念。

- 服务
- 容器
- 共享对象

## <a name="service"></a>服务

Fluid 客户端需要一个集中式服务，所有连接的客户端都使用该服务来发送和接收操作。 在应用程序中使用 Fluid 时，必须使用与要连接到的底层服务相对应的正确包。

对于 Azure Fluid Relay 服务，此包为 @fluidframework/azure-client。 此包有助于通过 Azure Fluid Relay 创建和加载托管在 Azure 上的 Fluid 容器。

## <a name="container"></a>容器

容器是 Fluid 中的主要封装单元。 它由一组共享对象和支持 API 组成，用于管理容器及其中对象的生命周期。

创建新容器是由客户端驱动的操作，容器生存期与存储在支持服务器上的数据绑定。 获取现有容器时，必须考虑容器的先前状态。

有关容器的详细信息，请参阅 flowframework.com 上的 [Containers](https://fluidframework.com/docs/build/containers/)（容器）。

## <a name="shared-objects"></a>共享对象

共享对象是一种对象类型，它通过公开特定的 API 来支持协作数据。 许多共享对象可以存在于容器的上下文中，并且可以通过静态或动态方式创建。 分布式数据结构 (DDS) 和 DataObject 都是共享对象类型。

有关详细信息，请参阅 fluidframework.com 上的 [Data modeling](https://fluidframework.com/docs/build/data-modeling/)（数据建模）。

## <a name="package-structure"></a>包结构

使用 Fluid 进行生成时，将使用两个主要包。 fluid-framework 包和特定于服务的客户端包，例如 azure-client。

有关详细信息，请参阅 fluidframework.com 上的 [Packages](https://fluidframework.com/docs/build/packages/)（包）。

### <a name="the-fluid-framework-package"></a>fluid-framework 包

fluid-framework 包是核心 Fluid API 的集合，便于生成和使用应用程序。 此包包含所有通用类型定义以及所有基元共享对象。

### <a name="the-fluidframeworkazure-client-package"></a>@fluidframework/azure-client 包

@fluidframework/azure-client 包提供一个 API，用于连接到 Azure Fluid Relay 服务实例以创建和加载 Fluid 容器。 有关如何使用此 API 的详细信息，请参阅[如何：连接到 Azure Fluid Relay 服务](../how-tos/connect-fluid-azure-service.md)。
