---
title: 在 Azure Arc 上配置 PostgreSQL 超大规模服务器组的 Postgres 引擎服务器参数
titleSuffix: Azure Arc enabled data services
description: 在 Azure Arc 上配置 PostgreSQL 超大规模服务器组的 Postgres 引擎服务器参数
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311051"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>为已启用 Azure Arc 的 PostgreSQL 超大规模设置数据库引擎设置

本文档介绍将 PostgreSQL 超大规模服务器组的数据库引擎设置设置为自定义（非默认）值的步骤。 有关可设置哪些数据库引擎参数及其默认值的详细信息，请参阅 [此处](https://www.postgresql.org/docs/current/runtime-config.html)的 PostgreSQL 文档。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> 预览不支持设置以下参数： 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>语法

用于配置数据库引擎设置的命令的常规格式为：

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>显示当前自定义值

### <a name="with-azure-data-cli-azdata-command"></a>With [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 命令

```console
azdata arc postgres server show -n <server group name>
```

例如：

```console
azdata arc postgres server show -n postgres01
```

此命令返回服务器组的规范，你可以在其中看到你所设置的参数。 如果没有 engine\settings 部分，则表示所有参数都按其默认值运行：

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>With kubectl 命令

请按照以下步骤操作。

1. 检索服务器组的自定义资源定义类型

   运行：

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   例如：

   ```console
   azdata arc postgres server show -n postgres01
   ```

   此命令返回服务器组的规范，你可以在其中看到你所设置的参数。 如果没有 engine\settings 部分，则表示所有参数都按其默认值运行：

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   在输出结果中，查找字段 `kind` 并将值保留为，例如： `postgresql-12` 。

2. 描述与服务器组相对应的 Kubernetes 自定义资源 

   命令的常规格式为：

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   例如：

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   如果为引擎设置设置了自定义值，则将返回这些值。 例如：

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   如果没有为任何引擎设置设置自定义值，则的 "引擎设置" 部分将为 `resultset` 空，如下所示：

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>设置引擎设置的自定义值

以下命令将 PostgreSQL 超大规模的协调器节点和辅助角色节点的参数设置为相同的值。 目前还不能为服务器组中的每个角色设置参数。 也就是说，不能将给定参数配置为协调器节点上的特定参数，也不能配置为辅助角色节点的其他值。

### <a name="set-a-single-parameter"></a>设置单个参数

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>使用单个命令设置多个参数

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>将参数重置为其默认值

若要将参数重置为其默认值，请在不指示值的情况下设置。 

例如：

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>将所有参数重置为其默认值

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>特殊注意事项

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>设置一个参数，该参数的值包含逗号、空格或特殊字符

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>在参数值中传递环境变量

环境变量应在 "' ' 内进行包装，以便在设置该变量之前不会对其进行解析。

例如： 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>后续步骤
- 阅读[ (向) 服务器组添加辅助角色节点](scale-out-postgresql-hyperscale-server-group.md)
- 阅读有关[扩展或缩减 (增加/减少服务器组) 的内存/vcore](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
