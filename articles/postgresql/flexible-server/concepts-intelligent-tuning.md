---
title: 智能优化 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍 Azure Database for PostgreSQL 灵活服务器中的智能优化功能。
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: ca4dbea3701007d29327b5f8f26e0566f77b0c13
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455903"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>在 Azure Database for PostgreSQL 灵活服务器中执行智能优化

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器和智能优化目前为预览版。

适用于：Azure Database for PostgreSQL 灵活服务器 11 和更高版本。

Azure Database for PostgreSQL 灵活服务器中的智能优化功能提供了一种自动提高数据库性能的方法。 智能优化可根据使用模式和值自动调整 `checkpoint_completion_target`、`min_wal_size` 和 `max_wal_size` 参数。 它每隔 30 分钟查询一次数据库的统计信息，并持续进行调整以优化性能，并且无需进行任何交互。

智能优化是一项选择加入功能，因此它在服务器上默认处于非活动状态。 它可用于单一数据库，但不是全局可用。 在一个数据库上启用该功能不会在所有连接的数据库上启用它。

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>使用 Azure 门户启用智能优化

1. 登录到 Azure 门户，选择 Azure Database for PostgreSQL 服务器。
2. 在菜单的“设置”部分中选择“服务器参数” 。
3. 搜索智能优化参数。
4. 将值设置为 True，然后选择“保存” 。

允许第一批数据在 azure_sys 数据库中最多保留 35 分钟。

## <a name="information-about-intelligent-tuning"></a>有关智能优化的信息

在给定时间内，智能优化围绕三个主要参数运行：`checkpoint_completion_target`、`min_wal_size` 和 `max_wal_size`。

这三个参数主要影响： 

* 检查点持续时间。
* 检查点频率。
* 同步持续时间。

智能优化是双向运行的。 它会尝试在高工作负载期间降低持续时间，在空闲段增加持续时间。 这样，你就可以在困难时段获得个性化的结果，而无需手动更新。

## <a name="limitations-and-known-issues"></a>限制和已知问题

* 智能优化只会在特定的范围内进行优化。 此功能可能不会进行任何更改。
* 查询中已删除的数据库可能会导致该功能在执行改进时出现轻微延迟。
* 该功能目前只在存储部分进行优化。
