---
title: 快速入门：创建 Redis Enterprise 缓存
description: 本快速入门介绍如何在企业层中创建 Azure Cache for Redis 的实例
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 7733f9df7f314dfdc0369075b96477e301825ce6
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537318"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>快速入门：创建 Redis Enterprise 缓存

Azure Cache for Redis 企业层在 Azure 上提供了完全集成和托管的 [Redis Enterprise](https://redislabs.com/redis-enterprise/)。 这些新的层级为：

* 企业层，它在虚拟机上使用易失存储器 (DRAM) 存储数据
* Enterprise Flash，使用易失存储器和非易失存储器（NVMe 或 SSD）存储数据。

## <a name="prerequisites"></a>先决条件

在开始之前，你需要一个 Azure 订阅。 如果没有帐户，请创建一个[帐户](https://azure.microsoft.com/)。 有关详细信息，请参阅[企业层的特殊注意事项](cache-overview.md#special-considerations-for-enterprise-tiers)。

## <a name="create-a-cache"></a>创建缓存

1. 若要创建缓存，请登录到 **Azure 门户** 并选择“创建资源”  。

1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis":::

1. 在“新建 Redis 缓存”页上配置新缓存的设置。

   | 设置      |  选择值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 |
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
   | **DNS 名称** | 输入在区域中唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名将为 \<DNS name\>.\<Azure region\>.redisenterprise.cache.azure.net。 |
   | **位置** | 单击下拉箭头并选择一个位置。 | 企业层在所选 Azure 区域中可用。 |
   | **缓存类型** | 下拉并选择 Enterprise 或 Enterprise Flash 层和大小 。 |  此层决定可用于缓存的大小、性能和功能。 |

   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="企业层“基本”选项卡":::

   > [!IMPORTANT]
   > 在继续之前，请务必选择“条款”。
   >

1. 在完成时选择“下一步:网络”并跳过。

1. 对于非群集缓存，请选择“下一步: 高级”，并将“群集策略”设置为“企业”。 如果打算在不使用 TLS 的情况下连接到新的缓存，请启用“仅限非 TLS 访问”。 但是，建议不要禁用 TLS。

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="显示企业层“高级”选项卡的屏幕截图。":::

   > [!NOTE]
   > Redis Enterprise 支持两个群集策略。 通过常规 Redis API 使用“企业”策略访问缓存，通过 OSS 群集 API 访问 OSS。
   >

   > [!NOTE]
   > 创建缓存实例后，无法更改模块。 此设置为“仅创建”。
   >

1. 在完成时选择“下一步:标记”并跳过。

1. 在完成时选择“下一步:查看 + 创建”。

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="企业层“查看 + 创建”选项卡":::

1. 检查设置，然后选择“创建”。

   创建缓存需要一些时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。 如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Cache for Redis 的企业层实例。

> [!div class="nextstepaction"]
> [创建使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)
