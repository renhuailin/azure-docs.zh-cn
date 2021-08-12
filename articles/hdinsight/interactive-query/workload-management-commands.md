---
title: Hive LLAP 工作负荷管理命令
titleSuffix: Azure HDInsight
description: Hive LLAP 工作负荷管理命令
ms.service: hdinsight
ms.topic: reference
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: dc05ae5ec7cad35d5cda549e654caf3d44d91a03
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482183"
---
# <a name="hive-llap-workload-management-commands"></a>Hive LLAP 工作负荷管理命令

可以借助以下 Hive 命令来控制和管理工作负荷管理功能。 这些命令类似于现有的 ALTER、CREATE、DROP 和 SHOW 语句。

## <a name="alter-mapping"></a>更改映射 
更改对某个资源池的查询的路由。 
#### <a name="syntax"></a>语法 
```hql
ALTER { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ]
```
#### <a name="example"></a>示例 
```hql
ALTER USER MAPPING 'hive' IN demo_plan TO etl WITH ORDER 1;
```

## <a name="alter-pool"></a>更改池 
修改查询池属性、添加触发器，以及删除触发器。 
#### <a name="syntax"></a>语法 
```hql
ALTER POOL plan_name.pool_path [ SET {property=value, ... } | UNSET { property, ... } ];
ALTER POOL plan_name.pool_path [ ADD | DROP ] TRIGGER name;
```
#### <a name="example"></a>示例 
```hql
ALTER POOL demo_plan.default ADD TRIGGER defaultToETL;
```

## <a name="alter-resource-plan"></a>更改资源计划 
启用、禁用、激活、验证或更改计划。 
#### <a name="syntax"></a>语法 
```hql
ALTER RESOURCE PLAN name [ VALIDATE | DISABLE | ENABLE | ACTIVATE | RENAME TO another_name | SET {property=value, ... } | UNSET {property, ... } ];
```
#### <a name="example"></a>示例 
```hql
ALTER RESOURCE PLAN demo_plan SET DEFAULT POOL=etl, QUERY_PARALLELISM=3;
```

## <a name="alter-trigger"></a>更改触发器 
将触发器添加到资源池或从中删除触发器。 
#### <a name="syntax"></a>语法 
```hql
ALTER TRIGGER plan_name.name { ADD TO | DROP FROM } { POOL path | UNMANAGED };
```
#### <a name="example"></a>示例 
```hql
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;
```

## <a name="create-mapping"></a>创建映射 
将查询路由到资源池。 
#### <a name="syntax"></a>语法 
```hql
CREATE { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ];
```
#### <a name="example"></a>示例 
```hql
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
```

## <a name="create-pool"></a>创建池 
为某个资源计划创建和添加查询池。 
#### <a name="syntax"></a>语法 
```hql
CREATE POOL plan_name.path WITH ALLOC_FRACTION = decimal, QUERY_PARALLELISM = num, [ SCHEDULING_POLICY = scheduling_value ];
```
#### <a name="example"></a>示例 
```hql
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
```

## <a name="create-resource-plan"></a>创建资源计划 
创建资源计划 
#### <a name="syntax"></a>语法 
```hql
CREATE RESOURCE PLAN plan_name [ WITH QUERY PARALLELISM=number | LIKE name];
```
#### <a name="example"></a>示例 
```hql
CREATE RESOURCE PLAN demo_plan;
```

## <a name="create-trigger"></a>创建触发器 
创建触发器并将它添加到资源计划。 
#### <a name="syntax"></a>语法 
```hql
CREATE TRIGGER plan_name.name WHEN condition DO action;
```
#### <a name="example"></a>示例 
```hql
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
```

## <a name="disable-workload-management"></a>禁用工作负荷管理 
停用活动的资源计划。 
#### <a name="syntax"></a>语法 
```hql
DISABLE WORKLOAD MANAGEMENT;
```
#### <a name="example"></a>示例 
```hql
DISABLE WORKLOAD MANAGEMENT
```

## <a name="drop-mapping"></a>删除映射 
删除资源计划中的映射。 
#### <a name="syntax"></a>语法 
```hql
DROP { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name;
```
#### <a name="example"></a>示例 
```hql
DROP USER MAPPING 'hive' IN demo_plan;
```

## <a name="drop-pool"></a>删除池 
删除资源计划中的查询池。 
#### <a name="syntax"></a>语法 
```hql
DROP POOL plan_name.pool_path;
```
#### <a name="example"></a>示例 
```hql
CREATE POOL demo_plan.etl;
```

## <a name="drop-resource-plan"></a>删除资源计划 
删除资源计划。 
#### <a name="syntax"></a>语法 
```hql
DROP RESOURCE PLAN plan_name;
```
#### <a name="example"></a>示例 
```hql
DROP RESOURCE PLAN demo_plan;
```

## <a name="drop-trigger"></a>删除触发器 
从资源计划中删除触发器。 
#### <a name="syntax"></a>语法 
```hql
DROP TRIGGER plan_name.trigger_name;
```
#### <a name="example"></a>示例 
```hql
DROP TRIGGER demo_plan.defaultToETL;
```

## <a name="replace-resource-plan-with"></a>将资源计划替换为 
将一个资源计划的内容替换为另一个资源计划的内容。 
#### <a name="syntax"></a>语法 
```hql
REPLACE RESOURCE PLAN name1 WITH name2; 
REPLACE ACTIVE RESOURCE PLAN name1 WITH name2;
```
#### <a name="example"></a>示例 
```hql
REPLACE RESOURCE PLAN rp_plan1 WITH rp_plan2;
```

## <a name="show-resource-plan"></a>显示资源计划 
列出计划内容。 
#### <a name="syntax"></a>语法 
```hql
SHOW RESOURCE PLAN plan_name;
```
#### <a name="example"></a>示例 
```hql
SHOW RESOURCE PLAN demo_plan;
```

## <a name="show-resource-plans"></a>显示资源计划 
列出所有资源计划。 
#### <a name="syntax"></a>语法 
```hql
SHOW RESOURCE PLANS;
```
#### <a name="example"></a>示例 
```hql
SHOW RESOURCE PLANS;
```
