---
title: Azure 流分析中的 Azure Synapse Analytics 输出
description: 本文介绍作为 Azure 流分析输出的 Azure Synapse Analytics。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 6c61f378dd9121c727fc245d177e11921a8a8e26
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094377"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure 流分析中的 Azure Synapse Analytics 输出

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) 是一种无限制的分析服务，它将企业数据仓库和大数据分析结合在一起。 

Azure 流分析作业可以输出到 Azure Synapse Analytics 中的专用 SQL 池表，并可处理高达 200 MB/秒的吞吐量速率。这可支持报表和仪表板等工作负荷带来的最苛刻的实时分析和热路径数据处理需求。  

专用 SQL 池表必须存在，然后才能将其作为输出添加到流分析作业。 表架构必须与作业输出中的字段及类型匹配。 

> [!NOTE] 
> 若要使用 Azure Synapse Analytics 作为输出，请确保在作业级别而不是输出级别配置存储帐户。 若要更改存储帐户设置，请在流分析作业的“配置”菜单中转到“存储帐户设置”。 仅使用支持表的存储帐户类型：常规用途 V2 和常规用途 V1。 仅选择标准层。 此方案不支持高级层。

## <a name="output-configuration"></a>输出配置

下表列出了用于创建 Azure Synapse Analytics 输出的属性名称及其说明。

|属性名称|说明|
|-|-|
|输出别名 |在查询中使用的友好名称，用于将查询输出定向到此数据库。 |
|数据库 |专用 SQL 池名称（将向该池发送输出）。 |
|服务器名称 |Azure Synapse 服务器名称。  |
|用户名 |对数据库拥有写入访问权限的用户名。 流分析仅支持 SQL 身份验证。 |
|密码 |用于连接到数据库的密码。 |
|表  | 将写入输出的表名称。 表名称区分大小写。 此表的架构应与字段数量以及作业输出生成的字段类型完全匹配。|

## <a name="next-steps"></a>后续步骤

* [使用托管标识访问 Azure 流分析作业的 Azure SQL 数据库或 Azure Synapse Analytics（预览）](sql-database-output-managed-identity.md)
* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
