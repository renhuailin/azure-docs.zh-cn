---
title: 将流分析作业连接到 Azure 虚拟网络 (VNET) 中的资源
description: 本文介绍如何连接 Azure 流分析作业与 VNET 中的资源。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878233"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>将流分析作业连接到 Azure 虚拟网络 (VNet) 中的资源

流分析作业对输入和输出 Azure 资源建立出站连接，以实时处理数据并生成结果。 这些输入和输出资源（例如，Azure 事件中心和 Azure SQL 数据库）可以位于 Azure 防火墙后面或 Azure 虚拟网络 (VNet) 中。 流分析服务是从不能在网络规则中直接包含的网络上运行的。

但是，在这种情况下，可以通过两种方式将流分析作业安全连接到输入和输出资源。
* 在流分析群集中使用专用终结点。
* 使用与‘允许受信任的服务’网络设置耦合的托管标识身份验证模式。

流分析作业不接受任何入站连接。

## <a name="private-endpoints-in-stream-analytics-clusters"></a>流分析群集中的专用终结点。
[流分析群集](./cluster-overview.md)是单租户专用的计算群集，可在其中运行流分析作业。 可以在流分析群集中创建托管专用终结点，这允许在群集上运行的任何作业与输入和输出资源建立安全出站连接。

在流分析群集中创建专用终结点的操作分为[两个步骤](./private-endpoints.md)。 此选项最适合用于大中型流式处理工作负荷，因为流分析群集的最小大小为 36 SU（不过，在开发、测试和生产等各种订阅或环境中的不同作业可以共享这 36 SU）。

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>使用‘允许受信任的服务’配置的托管标识身份验证
某些 Azure 服务提供“允许受信任的 Microsoft 服务”网络设置，启用此设置后，流分析作业可以使用强身份验证安全地连接到资源。 此选项允许作业连接到输入和输出资源，而无需流分析群集和专用终结点。 配置作业以使用此技术的操作分为 2 个步骤：
* 在流分析作业中配置输入或输出时使用托管标识身份验证模式。
* 通过将 Azure 角色分配给作业的系统分配的托管标识，授予特定流分析作业对目标资源的显式访问权限。 

启用“允许受信任的 Microsoft 服务”不会授予对任何作业的总访问权限。 这样就可以完全控制哪些特定流分析作业可以安全地访问资源。 

可以使用此技术将作业连接到以下 Azure 服务：
1. [Blob 存储或 Azure Data Lake Storage Gen2](./blob-output-managed-identity.md) - 可以是作业的存储帐户、流式输入或输出。
2. [Azure 事件中心](./event-hubs-managed-identity.md) - 可以是作业的流式处理输入或输出。

如果作业需要连接到其他输入或输出类型，则可以使用 Azure Functions 先从流分析写入到事件中心输出，然后写入到所选择的任何目标。 如果想要从流分析直接写入到 VNet 或防火墙保护的其他输出类型，则唯一的选择是使用流分析群集中的专用终结点。

## <a name="next-steps"></a>后续步骤

* [在流分析群集中创建和删除专用终结点](./private-endpoints.md)
* [使用托管标识身份验证连接到 VNet 中的事件中心](./event-hubs-managed-identity.md)
* [使用托管标识身份验证连接到 VNet 中的 Blob 存储和 ADLS Gen2](./blob-output-managed-identity.md)