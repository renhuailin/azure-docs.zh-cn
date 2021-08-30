---
title: 显示已启用 Arc 的超大规模 PostgreSQL 服务器组的配置
titleSuffix: Azure Arc-enabled data services
description: 显示已启用 Arc 的超大规模 PostgreSQL 服务器组的配置
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 727c792daa20c392a87f7c57100b72ae79a971f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733502"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>显示已启用 Arc 的超大规模 PostgreSQL 服务器组的配置

本文介绍如何显示服务器组的配置。 文章预测你可能会问自己的一些问题，并解答问题，以此进行说明。 有时问题可能会有多个有效答案。 本文介绍最常见或最有用的答案。 文章按主题对这些问题进行分组：

- 从 Kubernetes 的角度看
- 从已启用 Azure Arc 的数据服务的角度看

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>从 Kubernetes 的角度看

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL 使用多少个 Pod？

列出 Postgres 类型的 Kubernetes 资源。 运行以下命令：

```console
kubectl get postgresqls [-n <namespace name>]
```

此命令的输出显示已创建的服务器组的列表。 每个服务器组会显示 Pod 数。 例如：

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

此示例显示已创建 2 个服务器组，每个服务器组在 3 个 Pod 上运行（1 个协调器 + 2 个工作器）。 这意味着在此 Azure Arc 数据控制器中创建的服务器组使用 6 个 Pod。

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>已启用 Azure Arc 的超大规模 PostgreSQL 服务器组使用哪些 Pod？

运行：

```console
kubectl get pods [-n <namespace name>]
```

这会返回 Pod 列表。 根据给这些服务器组的命名可以看到服务器组所使用的 Pod。 例如：

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

在此示例中，托管已创建的两个服务器组的六个 Pod 是：
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>哪个服务器组 Pod 用于服务器组的哪个角色？

以 `-0` 为后缀的任何 Pod 名称表示协调器节点。 以 `-x` 为后缀（其中后缀 为 1 或更大的数字）的任何节点名称表示工作器节点。 在上述示例中：
- 协调器为：`postgres01-0`、`postgres02-0`
- 工作器为：`postgres01-2`、`postgres01-2`、`postgres02-1`、`postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Pod 的状态是什么？

运行 `kubectl get pods` 并查看 `STATUS` 列

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>使用了哪些永久性卷声明（PVC）？ 

如果要了解使用了哪些 PVC 以及哪些 PVC 用于数据、日志和备份，请运行： 

```console
kubectl get pvc [-n <namespace name>]
```

默认情况下，PVC 的名称前缀表示其用途：

- `backups-`...：是用于备份的 PVC
- `data-`...：是用于数据文件的 PVC
- `logs-`...：是用于事务日志/WAL 文件的 PVC

例如：

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>从已启用 Azure Arc 的数据服务的角度看：

* Arc 数据控制器中创建了多少个服务器组？
* 服务器组的名称是什么？
* 服务器组使用多少工作器节点？
* 服务器组运行何种版本的 Postgres？

使用以下任一命令。
- 使用 kubectl：

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   例如，它将生成以下输出，其中每行都是 `servergroup`。 下面显示的名称结构形式为：

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   以上输出显示了 2 个 Postgres 版本为 12 的服务器组。 如果版本为 Postgres 11，则 CRD 的名称为 postgresql-11.arcdata.microsoft.com。

   每个服务器组在 3 个节点/Pod 上运行：1 个协调器和 2 个工作器。

- **使用 Azure CLI (az)：**

运行以下命令。 输出显示的信息与运行 kubectl 显示的信息类似：

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>使用多少内存和 vCore 以及为服务器组创建了哪些扩展？

运行以下任一命令

使用 kubectl：

使用 kubectl 描述 Postgres 资源。 为此，需要其种类（如上所示，对应 Postgres 版本的 Kubernetes 资源 (CRD) 的名称）和服务器组的名称。
此命令的一般格式为：

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

例如：

```console
kubectl describe postgresql-12/postgres02
```

此命令显示服务器组的配置：

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>2020 年 10 月之前的版本，上述示例中的 `Workers` 是 `Shards`。 有关详细信息，请参阅[发行说明 - 已启用 Azure Arc 的数据服务（预览版）](release-notes.md)。

我们来了解以上所示 `servergroup` 说明中的一些具体兴趣点。 它告诉我们哪些有关此服务器组的内容？

- 服务器组使用 Postgres 版本 12： 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- 服务器组在 2020 年 8 月期间创建：
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- 此服务器组中创建了两个 Postgres 扩展：`citus` 和 `pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- 服务器组使用两个工作器节点
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- 服务器组保证每个节点使用 1 个 cpu/vCore 和 512MB Ram。 服务器组将使用 4 个以上 cpu/Vcore 和 1024MB 内存：
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - 服务器组可供查询，不存在任何问题。 所有节点都已启动并在运行：
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**使用 Azure CLI (az)：**

此命令的一般格式为：

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

例如：

```azurecli
az postgres arc-server show -n postgres02 --k8s-namespace <namespace> --use-k8s
```

返回下列输出，其格式与内容与 kubectl 返回的输出十分相似。

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>后续步骤
- [阅读已启用 Azure Arc 的超大规模 PostgreSQL 的概念](concepts-distributed-postgres-hyperscale.md)
- [阅读如何横向扩展（添加工作器节点）服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
- [阅读如何纵向扩展/缩减（增加或减少内存和/或 vCore）服务器组](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [阅读存储配置](storage-configuration.md)
- [阅读如何监视数据库实例](monitor-grafana-kibana.md)
- [在已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中使用 PostgreSQL 扩展](using-extensions-in-postgresql-hyperscale-server-group.md)
- [为已启用 Azure Arc 的 PostgreSQL 超大规模服务器组配置安全性](configure-security-postgres-hyperscale.md)
