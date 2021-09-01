---
title: 横向扩展和横向缩减 Azure Database for PostgreSQL 超大规模服务器组
description: 横向扩展和横向缩减 Azure Database for PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b3e7df998d32317763c6a0de7c0e7c1cc2f2420b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733508"
---
# <a name="scale-out-and-in-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>通过增加更多的工作器节点，横向扩展和横向缩减已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
本文档介绍了如何横向扩展和横向缩减已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。 本文档通过讲解一种方案来实现此目的。 **如果不想浏览方案，只是希望阅读如何横向扩展，请跳转到“[横向扩展](#scale-out)** ”或“[横向缩减]()”段落。

当你将 Postgres 实例（超大规模 Postgres 工作器节点）添加到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组时，便可以横向扩展。

当你从已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中删除 Postgres 实例（超大规模 Postgres 工作器节点）时，便可以横向缩减。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>入门
如果已熟悉了已启用 Azure Arc 的超大规模 PostgreSQL 或 Azure Database for PostgreSQL 超大规模 (Citus) 的缩放模型，则可以跳过这一段落。 如果不熟悉，建议首先在 Azure Database for PostgreSQL 超大规模 (Citus) 的文档页中阅读关于这种缩放模型的内容。 Azure Database for PostgreSQL 超大规模 (Citus) 是在 Azure 中作为服务托管的技术（平台即服务，也称 PAAS），而不是作为已启用 Azure Arc 的数据服务的一部分提供的技术：
- [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
- [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
- [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [表共置](../../postgresql/concepts-hyperscale-colocation.md)
- [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

## <a name="scenario"></a>场景
此方案是指在[创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](create-postgresql-hyperscale-server-group.md)文档中作为示例而创建的超大规模 PostgreSQL 服务器组。

### <a name="load-test-data"></a>加载测试数据
该方案使用了公开可用 GitHub 数据示例，可从 [Citus Data 网站](https://www.citusdata.com/)获取（Citus Data 隶属于 Microsoft）。

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>连接到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

##### <a name="list-the-connection-information"></a>列出连接信息
首先获取连接信息，以此连接到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组：此命令的常规格式为
```azurecli
az postgres arc-server endpoint list -n <server name>  --k8s-namespace <namespace> --use-k8s
```
例如： 。
```azurecli
az postgres arc-server endpoint list -n postgres01  --k8s-namespace <namespace> --use-k8s
```

示例输出：

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>使用选择的客户端工具进行连接。

运行以下查询，验证当前是否有两个（或更多个）超大规模工作器节点，每个节点对应一个 Kubernetes Pod：

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>创建一个示例架构
通过运行以下查询来创建两个表：

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB 是 PostgreSQL 中采用二进制格式的 JSON 数据类型。 它在单个列中存储一个灵活的架构，并采用 PostgreSQL。 该架构采用 GIN 索引对其中的每个键和值编制索引。 通过 GIN 索引,可以使用各种条件快速轻松地直接查询该有效负载。 在加载数据之前，让我们继续创建几个索引：

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

要对标准表分片，请对每个表运行查询。 指定要分片的表，并指定要用于分片的键。 我们将在 user_id 上对事件和用户表进行分片：

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>加载示例数据
使用 COPY ... FROM PROGRAM 加载数据：

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>查询数据
现在，使用两个节点测量简单查询所用的时间：

```sql
SELECT COUNT(*) FROM github_events;
```
记下查询执行时间。


## <a name="scale-out"></a>向外扩展
横向扩展命令的一般格式为：
```azurecli
az postgres arc-server edit -n <server group name> -w <target number of worker nodes> --k8s-namespace <namespace> --use-k8s
```


在此示例中，我们将运行以下命令以将工作器节点数从 2 个增加到 4 个：

```azurecli
az postgres arc-server edit -n postgres01 -w 4 --k8s-namespace <namespace> --use-k8s 
```

添加节点后，你将看到该服务器组的“挂起”状态。 例如：
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

节点可用后，超大规模分片再平衡器会自动运行，然后将数据重新分发到新节点。 横向扩展操作是一个联机操作。 在添加节点并跨节点重新分发数据时，数据仍可用于查询。

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>验证服务器组的新形状（可选）
使用以下任一方法来验证服务器组现在是否正在使用添加的附加工作器节点。

#### <a name="with-azure-cli-az"></a>使用 Azure CLI (az)：

运行以下命令：

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

这条命令将退回命名空间中创建的服务器组的列表，并指示其工作器节点数。 例如：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>使用 kubectl：
运行以下命令：
```console
kubectl get postgresqls
```

这条命令将退回命名空间中创建的服务器组的列表，并指示其工作器节点数。 例如：
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```

#### <a name="with-a-sql-query"></a>使用 SQL 查询：
使用所选的客户端工具连接到你的服务器组，然后运行以下查询：

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>退回到方案

如果要与示例数据集比较选择计数查询的执行时间，请使用相同的计数查询。 它可用于四个工作器节点，无需在 SQL 语句中进行任何更改。

```sql
SELECT COUNT(*) FROM github_events;
```
记下执行时间。


> [!NOTE]
> 根据你的环境（例如，如果已将包含 `kubeadm` 的测试服务器组部署在单个节点 VM 上），可能会看到执行时间的适度改善。 若要更好地了解使用已启用 Azure Arc 的超大规模 PostgreSQL 可以实现的性能提升类型，请观看以下短视频：
>* [使用 Azure PostgreSQL 超大规模 (Citus) 实现高性能 HTAP](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [使用 Python 和 Azure PostgreSQL 超大规模 (Citus) 构建 HTAP 应用程序](https://www.youtube.com/watch?v=YDT8_riLLs0)

## <a name="scale-in"></a>缩小
要横向缩减（减少服务器组中工作器节点的数量），可以使用与横向扩展相同的命令，但需要指定较少的工作器节点数量。 删除的工作器节点是最新添加到服务器组的工作器节点。 当你运行此命令时，系统会将数据移出已删除的节点，并自动将其重新分发（重新平衡）到其余节点。 

横向缩减命令的一般格式为：
```azurecli
az postgres arc-server edit -n <server group name> -w <target number of worker nodes> --k8s-namespace <namespace> --use-k8s
```


横向缩减操作是一个联机操作。 当删除节点并在其余节点之间重新分发数据时，应用程序可以继续访问数据，而不会出现故障时间。

## <a name="next-steps"></a>后续步骤

- 了解如何[纵向扩展和缩减（内存、vCore）已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- 了解如何在已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中设置服务器参数
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作指南，以将数据分布到多个超大规模 PostgreSQL 节点，并受益于 Azure Database for Postgres 超大规模的所有强大功能。 :
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
