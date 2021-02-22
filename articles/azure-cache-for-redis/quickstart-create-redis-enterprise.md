---
title: 快速入门：创建 Redis Enterprise 缓存
description: 本快速入门介绍如何在企业层中创建 Azure Cache for Redis 的实例
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 50535ab9ac2a3541ade7b9abd8f355bbeaf6d4b3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364998"
---
# <a name="quickstart-create-a-redis-enterprise-cache-preview"></a>快速入门：创建 Redis Enterprise 缓存（预览版）

Azure Cache for Redis 企业层在 Azure 上提供了完全集成和托管的 [Redis Enterprise](https://redislabs.com/redis-enterprise/)。 此功能目前以预览版的形式提供。 此预览版中有两个新层：
* 企业层，它在虚拟机上使用易失存储器 (DRAM) 存储数据
* Enterprise Flash，使用易失存储器和非易失存储器（NVMe 或 SSD）存储数据。

## <a name="prerequisites"></a>先决条件

在开始之前，你需要一个 Azure 订阅。 如果没有帐户，请创建一个[帐户](https://azure.microsoft.com/)。 有关详细信息，请参阅[企业层要求](cache-overview.md#enterprise-and-enterprise-flash-tier-requirements)。

## <a name="create-a-cache"></a>创建缓存
1. 若要创建缓存，请使用预览版邀请中的链接登录到 Azure 门户，然后选择“创建资源”。

1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名将为 \<DNS name>.<Azure region>.redisenterprise.cache.azure.net。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 在预览期间，企业层在有限的 Azure 区域可用。 |
   | **缓存类型** | 下拉并选择 Enterprise 或 Enterprise Flash 层和大小 。 |  此层决定可用于缓存的大小、性能和功能。 |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="企业层基本设置":::

   > [!NOTE] 
   > 继续操作之前，请务必选中“条款”下的复选框。
   >

1. 在完成时选择“下一步:网络”并跳过。

   > [!NOTE] 
   > 专用链接选项即将推出，可能无法在你的区域立即使用。
   >

1. 在完成时选择“下一步:高级”，并将“群集策略”设置为 Enterprise 。
   
   可以保留默认设置或根据需要更改这些设置。 启用“仅允许通过 TLS 访问”时，必须使用 TLS 从应用程序访问新缓存。

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="企业层高级设置":::

   > [!NOTE] 
   > Enterprise Flash 层尚不支持 Redis 模块。 如果计划使用 Redis 模块，请确保选择企业层缓存。
   >
   
1. 在完成时选择“下一步:标记”并跳过。

1. 在完成时选择“下一步:查看 + 创建”。

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="企业层摘要":::

1. 检查设置并单击“创建”。
   
   创建缓存需要一些时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。 如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Cache for Redis 的企业层实例。

> [!div class="nextstepaction"]
> [创建使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)

