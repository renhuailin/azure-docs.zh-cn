---
title: 配置已启用 Azure Arc 的 SQL 托管实例
description: 配置已启用 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e84d5be7252f81c4e80d6070ada2151fcc3960f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743885"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>配置已启用 Azure Arc 的 SQL 托管实例

本文介绍如何配置已启用 Azure Arc 的 SQL 托管实例。


## <a name="configure-resources"></a>配置资源

### <a name="configure-using-cli"></a>使用 CLI 进行配置

可以使用 CLI，对已启用 Azure Arc 的 SQL 托管实例的配置进行编辑。 运行以下命令，以查看配置选项。 

```azurecli
az sql mi-arc edit --help
```

下面的示例设置了 CPU 内核和内存请求及限制。

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

若要查看对 SQL 托管实例所做的更改，可以使用以下命令查看配置 yaml 文件：

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>配置服务器选项

创建完成后，可以配置已启用 Azure Arc 的 SQL 托管实例的服务器配置设置。 本文介绍如何配置设置（例如启用或禁用 MSSQL 代理），以及如何启用特定的跟踪标志对方案进行故障排除。

若要更改任何设置，请执行以下步骤：

1. 创建包括目标设置的自定义 `mssql-custom.conf` 文件。 以下示例中启用了 SQL 代理和跟踪标识 1204.：

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. 将 `mssql-custom.conf` 文件复制到 `master-0` Pod 中的 `mssql-miaa` 容器中的 `/var/opt/mssql`。 将 `<namespaceName>` 替换为 Arc 命名空间名称。

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. 重启 SQL Server 实例。  将 `<namespaceName>` 替换为 Arc 命名空间名称。

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**已知的限制**
- 以上步骤需要 Kubernetes 群集管理员权限
