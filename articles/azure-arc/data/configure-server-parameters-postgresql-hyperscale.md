---
title: 在 Azure Arc 上为超大规模 PostgreSQL 服务器组配置 Postgres 引擎服务器参数
titleSuffix: Azure Arc-enabled data services
description: 在 Azure Arc 上为超大规模 PostgreSQL 服务器组配置 Postgres 引擎服务器参数
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e634bcc7d07cfba4016c8f2db323e78e9beda92a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737191"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>为已启用 Azure Arc 的 PostgreSQL 超大规模设置数据库引擎设置

本文档介绍将 PostgreSQL 超大规模服务器组的数据库引擎设置设置为自定义（非默认）值的步骤。 有关可以设置的数据库引擎参数及其默认值的详细信息，请参阅[此处](https://www.postgresql.org/docs/current/runtime-config.html)的 PostgreSQL 文档。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> 预览版不支持设置以下参数： 
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

用于配置数据库引擎设置的命令的一般格式为：

```azurecli
az postgres arc-server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-settings , --re}] {'<parameter name>=<parameter value>, ...'} --k8s-namespace <namespace> --use-k8s
```

## <a name="show-current-custom-values"></a>显示当前自定义值

### <a name="with-azure-data-cli-azdata-command"></a>使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 命令

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

例如：

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s 
```

此命令返回服务器组的规格，在其中可以看到你设置的参数。 如果没有 engine\settings 节，则表示所有参数都以其默认值运行：

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

### <a name="with-kubectl-command"></a>使用 kubectl 命令

遵循以下步骤。

1. 检索服务器组的自定义资源定义类型

   运行：

   ```azurecli
   az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
   ```

   例如：

   ```azurecli
   az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s
   ```

   此命令返回服务器组的规格，在其中可以看到你设置的参数。 如果没有 engine\settings 节，则表示所有参数都以其默认值运行：

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

   在输出结果中，找到字段 `kind` 并保留其现有值，例如：`postgresql-12`。

2. 描述对应于服务器组的 Kubernetes 自定义资源 

   该命令的一般格式为：

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   例如：

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   如果为引擎设置指定了自定义值，该命令将返回这些值。 例如：

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   如果没有为任何引擎设置指定自定义值，则 `resultset` 的 Engine Settings 节将是空的，如下所示：

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>指定引擎设置的自定义值

以下命令将超大规模 PostgreSQL 的协调器节点和工作器节点的参数设置为相同值。 目前还无法为服务器组中的每个角色设置参数。 也就是说，无法在协调器节点和工作器节点上将给定的参数配置为不同的特定值。

### <a name="set-a-single-parameter"></a>设置单个参数

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  <parameter name>=<parameter value> --k8s-namespace <namespace> --use-k8s
```

例如：

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  shared_buffers=8MB --k8s-namespace <namespace> --use-k8s
```

### <a name="set-multiple-parameters-with-a-single-command"></a>使用一条命令设置多个参数

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>=<parameter value>, <parameter name>=<parameter value>, --k8s-namespace <namespace> --use-k8s...'
```

例如：

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=50' --k8s-namespace <namespace> --use-k8s
```

### <a name="reset-a-parameter-to-its-default-value"></a>将参数重置为其默认值

若要将某个参数重置为其默认值，请设置该参数但不要指定值。 

例如：

```azurecli
az postgres arc-server edit -n postgres01 --k8s-namespace <namespace> --use-k8s --engine-settings  shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>将所有参数重置为其默认值

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '' -re --k8s-namespace <namespace> --use-k8s
```

例如：

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  '' -re --k8s-namespace <namespace> --use-k8s
```

## <a name="special-considerations"></a>特殊注意事项

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>设置一个值包含逗号、空格或特殊字符的参数

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>="<parameter value>"' --k8s-namespace <namespace> --use-k8s
```

例如：

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'custom_variable_classes = "plpgsql,plperl"' --k8s-namespace <namespace> --use-k8s
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>在参数值中传递环境变量

应将环境变量括在 "''" 中，以便在设置该变量之前不会对其进行求解。

例如： 

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'search_path = "$user"' --k8s-namespace <namespace> --use-k8s
```

## <a name="next-steps"></a>后续步骤
- 了解如何[横向扩展（添加工作器节点）](scale-out-in-postgresql-hyperscale-server-group.md)服务器组
- 了解如何[纵向扩展或缩减（增加/减少内存/vCore）](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)服务器组
