---
title: 使用 CLI（azdata 或 kubectl）纵向扩展和缩减 Azure Database for PostgreSQL 超大规模服务器组
description: 使用 CLI（azdata 或 kubectl）纵向扩展和缩减 Azure Database for PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687543"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>使用 CLI（azdata 或 kubectl）纵向扩展和缩减 Azure Database for PostgreSQL 超大规模服务器组



有时候你可能需要更改服务器组的特征或定义。 例如：

- 纵向扩展或缩减每个协调器或工作器节点使用的 vCore 数目
- 纵向扩展或缩减每个协调器或工作器节点使用的内存

本指南说明如何缩放 vCore 和/或内存。

纵向扩展或缩减服务器组的 vCore 或内存设置意味着可以设置每个 vCore 和内存设置的最小值和/或最大值。 如果要将服务器组配置为使用特定的 vCore 数目或特定的内存量，请将最小值设置设置为等于最大值设置。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>显示服务器组的当前定义

若要显示服务器组的当前定义并查看当前的 vCore 和内存设置，请运行以下命令之一：

### <a name="cli-with-azdata"></a>CLI azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> 如果创建了 PostgreSQL 版本 11 的服务器组，请改为运行 `kubectl describe postgresql-11/<server group name>`。

它将返回服务器组的配置。 如果已使用默认设置创建了服务器组，应会看到如下所示的定义：

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>解释服务器组的定义

在服务器组的定义中，每个节点的最小/最大 vCore 设置和每个节点的最小/最大内存设置显示在“计划”部分。  在该部分中，最大值设置保留在名为“限制”的子节中，最小值设置保留在名为“请求”的子节中。

如果设置的最小值设置与最大值设置不同，则配置可保证在需要时为服务器组分配所请求的资源。 它不会超过设置的限制。

服务器组实际使用的资源（Vcore 和内存）最大为最大值设置，并且取决于工作负荷和群集上可用的资源。 如果没有使用最大值来设置上限，则服务器组最多可能使用 Kubernetes 群集分配给 Kubernetes 节点（在该节点上规划服务器组）的所有资源。

这些 vCore 和内存设置适用于每个 PostgreSQL 超大规模节点（协调器节点和工作器节点）。 尚不支持分开设置协调器节点的定义和工作器节点的定义。

在默认配置中，仅将最小内存设置为 256Mi，因为它是运行 PostgreSQL 超大规模时建议使用的最小内存量。

> [!NOTE]
> 设置最小值不意味着服务器组将一定使用该最小值。 它意味着如果服务器组需要最小值，则保证为其至少分配这个最小值。 比如说我们设置 `--minCpu 2`。 这不意味着服务器组将始终使用至少 2 个 Vcore。 而是意味着，如果服务器组不需要那么多的 vCore，开始时可以使用少于 2 个的 Vcore，并且以后如果需要，保证可以为其至少分配 2 个 Vcore。 这意味着，Kubernetes 群集将资源分配给其他工作负荷，并且在服务器组需要时可以将 2 个 Vcore 分配给该服务器组。

>[!NOTE]
>在修改系统配置之前，请确保在[此处](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)熟悉 Kubernetes 资源模型

## <a name="scale-up-the-server-group"></a>纵向扩展服务器组

要设置的设置必须纳入为 Kubernetes 群集所设置的配置的范围内进行考虑。 请确保未设置 Kubernetes 群集无法满足的值。 这可能导致错误或不可预测的行为。 例如，如果在更改配置后，服务器组的状态长时间处于“正在更新”状态，则可能表示将以下参数设置为 Kubernetes 群集无法满足的值。 如果是这种情况，请还原更改或阅读 _troubleshooting_section。

例如，假设你要将服务器组的定义纵向扩展到：

- 最小 vCore = 2
- 最大 vCore = 4
- 最小内存 = 512Mb
- 最大内存 = 1Gb

可以使用以下两种方法中的一种：

### <a name="cli-with-azdata"></a>CLI azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> 下面的示例说明如何使用该命令。 在执行编辑命令之前，请确保将参数设置为 Kubernetes 群集可以遵循的值。

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

当显示以下内容时，说明命令成功执行：

```console
<name of your server group> is Ready
```

> [!NOTE]
> 有关这些参数的详细信息，请运行 `azdata arc postgres server edit --help`。

### <a name="cli-with-kubectl"></a>CLI kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

这会将你带到 vi 编辑器中，可以在其中导航并更改配置。 使用以下内容将所需的设置映射到规范中字段的名称：

> [!CAUTION]
> 下面的示例说明如何编辑配置。 更新配置之前，请确保将参数设置为 Kubernetes 群集可以遵循的值。

例如：
- Min vCore = 2 -> scheduling\default\resources\requests\cpu
- Max vCore = 4 -> scheduling\default\resources\limits\cpu
- Min memory = 512Mb -> scheduling\default\resources\requests\cpu
- Max Memory = 1Gb ->  scheduling\default\resources\limits\cpu

如果你不熟悉 vi 编辑器，请在[此处](https://www.computerhope.com/unix/uvi.htm)查看所需命令的说明：
- 编辑模式：`i`
- 用箭头移动
- _停止编辑：`esc`
- _退出而不保存：`:qa!`
- _保存后退出：`:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>显示服务器组的纵向扩展的定义

再次运行命令以显示服务器组的定义，并验证它的设置是否如你所愿：

### <a name="cli-with-azdata"></a>CLI azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> 如果创建了 PostgreSQL 版本 11 的服务器组，请改为运行 `kubectl describe postgresql-11/<server group name>`。


它将显示服务器组的新定义：

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>纵向缩减服务器组

若要纵向缩减服务器组，请执行相同的命令，但为要缩减的设置设置较小的值。 若要删除请求和/或限制，请将其值指定为空字符串。

## <a name="reset-to-default-values"></a>重置为默认值
若要将核心/内存限制/请求参数重置为其默认值，请对其进行编辑，并传递空字符串而非实际值。 例如，如果要重置核心限制 (cl) 参数，请运行以下命令：
- 在 Linux 客户端上：

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- 在 Windows 客户端上： 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>后续步骤

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
