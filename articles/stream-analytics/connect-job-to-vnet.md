---
title: '将流分析作业连接到 Azure 虚拟网络中的资源 (VNET) '
description: 本文介绍如何使用 VNET 中的资源连接 Azure 流分析作业。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: f140384ee2c78b38982c99dab5912bc451584a42
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898141"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>将流分析作业连接到 Azure 虚拟网络中的资源 (VNet) 

流分析作业对输入和输出 Azure 资源建立出站连接，以实时处理数据并生成结果。 这些输入和输出资源 (例如，Azure 事件中心和 Azure SQL Database) 可以位于 Azure 防火墙后面或 Azure 虚拟网络 (VNet) 中。 流分析服务不能直接包含在网络规则中的网络上运行。

但是，在这种情况下，可以通过两种方式将流分析作业安全连接到输入和输出资源。
* 在流分析群集中使用专用终结点。
* 使用托管标识身份验证模式与 "允许受信任的服务" 网络设置结合使用。

你的流分析作业不接受任何入站连接。

## <a name="private-endpoints-in-stream-analytics-clusters"></a>流分析群集中的专用终结点。
[流分析群集](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) 是单个租户专用的计算群集，可在其中运行流分析作业。 可以在流分析群集中创建托管的专用终结点，这允许在群集上运行的任何作业与输入和输出资源建立安全出站连接。

在流分析群集中创建专用终结点的操作分为 [两步](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)。 此选项最适合用于大中型流式处理工作负荷，因为流分析群集的最小大小为 36 SUs (，不过，在开发、测试和生产) 等各种订阅或环境中的不同作业可以共享 36 SUs。

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>"允许受信任的服务" 配置的托管标识身份验证
某些 Azure 服务提供 " **允许受信任的 Microsoft 服务** 网络" 设置，启用此功能后，流分析作业可以使用强身份验证安全地连接到资源。 此选项允许你将作业连接到输入和输出资源，而无需流分析群集和专用终结点。 将作业配置为使用此技术是一个两步操作：
* 在流分析作业中配置输入或输出时使用托管标识身份验证模式。
* 通过将 Azure 角色分配给作业的系统分配的托管标识，授予特定流分析作业对目标资源的显式访问权限。 

启用 " **允许受信任的 Microsoft 服务** " 不会授予对任何作业的总访问权限。 这使你可以完全控制哪些特定流分析作业可以安全地访问你的资源。 

你的作业可以使用此方法连接到以下 Azure 服务：
1. [Blob 存储或 Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) -可以是作业的存储帐户、流式输入或输出。
2. [Azure 事件中心](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) -可以是作业的流式处理输入或输出。

如果你的作业需要连接到其他输入或输出类型，则可以使用 Azure Functions 从流分析首次写入到事件中心输出，然后写入到你选择的任何目标。 如果要从流分析直接写入 VNet 或防火墙中保护的其他输出类型，则唯一的选择是使用流分析群集中的专用终结点。

## <a name="next-steps"></a>后续步骤

* [在流分析群集中创建和删除专用终结点](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [使用托管标识身份验证连接到 VNet 中的事件中心](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [使用托管标识身份验证连接到 VNet 中的 Blob 存储和 ADLS Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
