---
title: 配置已启用 Azure Arc 的 SQL 托管实例
description: 配置已启用 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 09/1/2021
ms.topic: how-to
ms.openlocfilehash: b95688eca33400956997b44bda43565454f82479
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481211"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>配置已启用 Azure Arc 的 SQL 托管实例

本文介绍如何配置已启用 Azure Arc 的 SQL 托管实例。


## <a name="configure-resources-such-as-cores-memory"></a>配置核心、内存等资源


### <a name="configure-using-cli"></a>使用 CLI 进行配置

可以使用 CLI，对已启用 Azure Arc 的 SQL 托管实例的配置进行编辑。 运行以下命令，以查看配置选项。 

```azurecli
az sql mi-arc edit --help
```

可使用以下命令为已启用 Azure Arc 的 SQL 托管实例更新可用内存和核心：

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

下面的示例设置了 CPU 内核和内存请求及限制。

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

若要查看对已启用 Azure Arc 的 SQL 托管实例所做的更改，可使用以下命令查看配置 YAML 文件：

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>配置服务器选项

创建完成后，可以配置已启用 Azure Arc 的 SQL 托管实例的服务器配置设置。 本文介绍如何配置设置（例如启用或禁用 MSSQL 代理），以及如何启用特定的跟踪标志对方案进行故障排除。


### <a name="enable-sql-server-agent"></a>启用 SQL Server 代理

默认情况下，SQL Server 代理处于禁用状态。 可通过运行以下命令将其启用：

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
示例：
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>启用跟踪标志

可按如下方式启用跟踪标志：
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```

