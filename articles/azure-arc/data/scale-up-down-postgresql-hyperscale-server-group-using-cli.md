---
title: 使用 CLI（az 或 kubectl）纵向扩展和缩减 Azure Database for PostgreSQL 超大规模服务器组
description: 使用 CLI（az 或 kubectl）纵向扩展和缩减 Azure Database for PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 8b2b64de8dd16e36b6956c289beda986d89a5c98
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733505"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-az-or-kubectl"></a>使用 CLI（az 或 kubectl）纵向扩展和缩减 Azure Database for PostgreSQL 超大规模服务器组

有时候你可能需要更改服务器组的特征或定义。 例如：

- 纵向扩展或缩减每个协调器或工作器节点使用的 vCore 数目
- 纵向扩展或缩减每个协调器或工作器节点使用的内存

本指南说明如何缩放 vCore 和/或内存。

纵向扩展或缩减服务器组的 vCore 或内存设置意味着可以设置每个 vCore 和内存设置的最小值和/或最大值。 如要将服务器组配置为使用特定的 vCore 数目或特定的内存量，请将最小值设置设为等于最大值设置。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>显示服务器组的当前定义

若要显示服务器组的当前定义并查看当前的 vCore 和内存设置，请运行以下命令之一：

### <a name="cli-with-azure-cli-az"></a>CLI 和 azure cli (az)

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```
### <a name="cli-with-kubectl"></a>CLI kubectl

```console
kubectl describe postgresql/<server group name> -n <namespace name>
```

它将返回服务器组的配置。 如果已使用默认设置创建了服务器组，应会看到如下所示的定义：

```json
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
...
```

## <a name="interpret-the-definition-of-the-server-group"></a>解释服务器组的定义

在服务器组的定义中，每个节点的最小/最大 vCore 数量设置和每个节点的最小/最大内存量设置显示在“计划”部分。 在该部分中，最大值设置保留在名为“限制”的子节中，最小值设置保留在“请求”子节中。 

如果设置的最小值设置与最大值设置不同，则配置可保证在需要时为服务器组分配所请求的资源。 它不会超过设置的限制。

服务器组实际使用的资源（vCore 和内存）最高不能超过最大值设置，具体取决于工作负荷和群集上的可用资源。 如果没有使用最大值来设置上限，则服务器组最多可能使用 Kubernetes 群集分配给 Kubernetes 节点（在该节点上规划服务器组）的所有资源。

这些 vCore 和内存设置适用于构成 PostgreSQL 超大规模服务器组的 Postgres 实例的每个角色：协调器和工作器。 你可以定义每个角色的请求和限制。 你可以定义每个角色的不同请求和限制设置。 它们还可能类似于你的需求。

在默认配置中，仅将最小内存设置为 256Mi，因为它是运行 PostgreSQL 超大规模时建议使用的最小内存量。

> [!NOTE]
> 设置最小值不意味着服务器组将一定使用该最小值。 它意味着如果服务器组需要最小值，则保证为其至少分配这个最小值。 比如说我们设置 `--minCpu 2`。 这不意味着服务器组将始终使用至少 2 个 Vcore。 而是意味着，如果服务器组不需要那么多的 vCore，开始时可以使用少于 2 个的 Vcore，并且以后如果需要，保证可以为其至少分配 2 个 Vcore。 这意味着，Kubernetes 群集将资源分配给其他工作负荷，并且在服务器组需要时可以将 2 个 Vcore 分配给该服务器组。 此外，纵向扩展和缩减不是一个联机操作，因为该操作需要重新启动 Kubernetes Pod。

>[!NOTE]
>在修改系统配置之前，请确保在[此处](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)熟悉 Kubernetes 资源模型

## <a name="scale-up-and-down-the-server-group"></a>纵向扩展和缩减服务器组

纵向扩展是指增加服务器组的 vCore 和/或内存设置的值。
纵向缩减是指减小服务器组的 vCore 和/或内存设置的值。

要设置的设置必须纳入为 Kubernetes 群集所设置的配置的范围内进行考虑。 请确保未设置 Kubernetes 群集无法满足的值。 这可能导致出错或发生不可预测的行为，如数据库实例不可用。 例如，如果在更改配置后，服务器组的状态长时间处于“正在更新”状态，则可能表示将以下参数设置为 Kubernetes 群集无法满足的值。 如果是这种情况，请还原更改或阅读 _troubleshooting_section。

应设置哪些设置？
- 若要设置最小 vCore，请设置 `--cores-request` 。
- 若要设置最大 vCore，请设置 `--cores-limit` 。
- 若要设置最小内存，请设置 `--memory-request`
- 若要设置最大内存，请设置 `--memory-limit`

如何指定应用此设置的角色？
- 若要配置协调器角色的设置，请指定 `coordinator=<value>`
- 若要为工作器配置设置（指定的设置将为所有工作器设置相同值），请指定 `worker=<value>`


> [!CAUTION]
> 使用 Kubernetes，配置限制设置而不配置相应的请求设置时，系统会强制让请求值与限制保持一致。 这可能会导致服务器组不可用，因此，如果没有提供充足资源的 Kubernetes 节点，则可能不会重新计划 Pod。 因此，为了避免出现这种情况，下面的示例演示了如何设置请求和限制设置。


**常规语法为：**

```azurecli
az postgres arc-server edit -n <servergroup name> --memory-limit/memory-request/cores-request/cores-limit <coordinator=val1,worker=val2> --k8s-namespace <namespace> --use-k8s
```

为内存设置指示的值是数字，其后是卷的单位。 例如，若要指示 1Gb，应指示 1024Mi 或 1Gi。
若要指示多个内核，只需传递没有单位的数字。 

### <a name="examples-using-the-azure-cli"></a>使用 Azure CLI 的示例

配置协调器角色，注意不可超过 2 个核心，而工作器不得超过 4 个核心：

```azurecli
 az postgres arc-server edit -n postgres01 --cores-request coordinator=1, --cores-limit coordinator=2  --k8s-namespace <namespace> --use-k8s
 az postgres arc-server edit -n postgres01 --cores-request worker=1, --cores-limit worker=4 --k8s-namespace <namespace> --use-k8s
```

或
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator=1,worker=1 --cores-limit coordinator=4,worker=4 --k8s-namespace <namespace> --use-k8s
```

> [!NOTE]
> 有关这些参数的详细信息，请运行 `az postgres arc-server edit --help`。

### <a name="example-using-kubernetes-native-tools-like-kubectl"></a>使用 Kubernetes 本机工具（如 `kubectl`）的示例

运行以下命令： 
```console
kubectl edit postgresql/<servergroup name> -n <namespace name>
```

这会将你带到 `vi` 编辑器中，你可以在其中执行导航并更改配置。 使用以下内容将所需的设置映射到规范中字段的名称：

> [!CAUTION]
> 下面的示例说明如何编辑配置。 更新配置之前，请确保将参数设置为 Kubernetes 群集可以遵循的值。

例如，如要将协调器和工作器角色的以下设置设置为下值：
- 最小 vCore = `2` 
- 最大 vCore = `4`
- 最小内存 = `512Mb`
- 最大内存 = `1Gb` 

你可以设置服务器组的定义，使其与以下配置匹配：

```json
  scheduling:
    default:
      resources:
        requests:
          memory: 256Mi
    roles:
      coordinator:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 512Mi
      worker:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 512Mi
```

如果你不熟悉 `vi` 编辑器，请在[此处](https://www.computerhope.com/unix/uvi.htm)查看所需命令的说明：
- 编辑模式：`i`
- 用箭头移动
- 停止编辑：`esc`
- 退出而不保存：`:qa!`
- 保存后退出：`:qw!`


## <a name="reset-to-default-values"></a>重置为默认值
若要将核心/内存限制/请求参数重置为其默认值，请对其进行编辑，并传递空字符串而非实际值。 例如，如要重置核心限制参数，请运行以下命令：

```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --k8s-namespace <namespace> --use-k8s
az postgres arc-server edit -n postgres01 --cores-limit coordinator='',worker='' --k8s-namespace <namespace> --use-k8s
```

或 
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --cores-limit coordinator='',worker='' --k8s-namespace <namespace> --use-k8s
```

## <a name="next-steps"></a>后续步骤

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
