---
title: 智能优化 - Azure Database for PostgreSQL - 灵活服务器
description: 本文介绍 Azure Database for PostgreSQL - 灵活服务器中的智能优化功能。
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: d16a1842c4dfed044a8ac865a3dcdcd6cbe05bf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778645"
---
# <a name="perform-intelligent-tuning-on-your-postgresql-flex-server"></a>在 PostgreSQL 灵活服务器上执行智能优化

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

> [!IMPORTANT]
> 智能优化目前处于预览阶段

**适用于：** Azure Database for PostgreSQL 灵活服务器 11 及更高版本

Azure Database for PostgreSQL 灵活服务器中的智能优化功能提供了一种自动提高数据库性能的方法。 通过我们的服务，智能优化会根据使用模式和值自动调整 Checkpoint_completion_target、min_wal_size 和 max_wal_size。 我们的服务每 30 分钟将查询一次数据库的统计信息，并会不断进行调整以优化数据库的性能，无需用户进行任何交互。

## <a name="enabling-intelligent-tuning"></a>启用智能优化

智能优化是一项选择加入功能，因此它在服务器上默认处于非活动状态。 优化适用于单个数据库，而并非全局，因此对某一数据库启用此功能并不会对连接的所有数据库启用。

### <a name="enable-intelligent-tuning-using-the-azure-portal"></a>使用 Azure 门户启用智能优化

1. 登录到 Azure 门户，选择 Azure Database for PostgreSQL 服务器。
2. 在菜单的“设置”部分中选择“服务器参数” 。
3. 搜索智能优化参数。
4. 将值设为 True 并保存。

允许第一批数据在 azure_sys 数据库中最多保留 35 分钟。

## <a name="information-about-intelligent-tuning"></a>有关智能优化的信息

在给定时间内，智能优化基于三个主要功能进行操作

* Checkpoint_completion_target
* Min_wal_size
* Max_wal_size

这三个变量主要影响以下方面

* 检查点持续时间
* 检查点频率
* 同步持续时间

智能优化是双向运作的，它会尝试在高工作负载期间降低持续时间，在空闲段增加持续时间。 这些规则是我们试图优化这些功能的方法，这样用户就可以在困难时期获得个性化的结果，而无需手动更新。

## <a name="limitations-and-known-issues"></a>限制和已知问题

* 优化仅在特定范围内发布，有可能不会进行任何更改
* 智能优化功能可能会因查询中删除的数据库而延迟，这可能会导致在执行改进时出现轻微延迟
  
优化目前只在存储部分进行，还未扩展到其他各种类别
