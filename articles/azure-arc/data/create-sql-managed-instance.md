---
title: 在 Azure Arc 上创建 Azure SQL 托管实例
description: 在 Azure Arc 上创建 Azure SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: bbf9ab64e1214a5b7676a8e5f506ba627a362198
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743868"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>在 Azure Arc 上创建 Azure SQL 托管实例

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]


## <a name="create-an-azure-sql-managed-instance"></a>创建 Azure SQL 托管实例

要查看 SQL 托管实例的 create 命令的可用选项，请使用以下命令：
```azurecli
az sql mi-arc create --help
```

如果要创建 SQL 托管实例，请使用以下命令：

```azurecli
az sql mi-arc create -n <instanceName> --k8s-namespace <namespace> --use-k8s
```

示例：

```azurecli
az sql mi-arc create -n sqldemo --k8s-namespace my-namespace --use-k8s
```
> [!NOTE]
>  名称长度必须少于 13 个字符，且符合 [DNS 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  当指定内存分配和 vCore 分配时，使用此公式来确保性能可以接受 - 在运行 SQL 托管实例的 Kubernetes 节点上每 1 个 vCore 应至少有 4 GB 的 RAM 容量。
>
>  如果要自动创建 SQL 实例并避免管理员密码的交互式提示，可以在运行 `az sql mi-arc create` 命令之前，将 `AZDATA_USERNAME` 和 `AZDATA_PASSWORD` 环境变量设置为所需的用户名和密码。
> 
>  如果在同一终端会话中使用 AZDATA_USERNAME 和 AZDATA_PASSWORD 创建了数据控制器，则 AZDATA_USERNAME 和 AZDATA_PASSWORD 的值也将用于创建 SQL 托管实例。

> [!NOTE]
> 如果使用间接连接模式，在 Kubernetes 中创建 Azure SQL 托管实例不会自动在 Azure 中注册资源。 以下文章介绍了注册资源的步骤： 
> - [将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>在 Azure Arc 上查看实例

如果要查看实例，请使用以下命令：

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

可以从此处复制外部 IP 和端口号，并使用你常用的工具连接到它，以便连接到 SQL server/AZURE SQL 实例，如 Azure Data Studio 或 SQL Server Management Studio。

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="next-steps"></a>后续步骤
- [连接到已启用 Azure Arc 的 SQL 托管实例](connect-managed-instance.md)
- [向 Azure 注册实例并上传有关实例的指标和日志](upload-metrics-and-logs-to-azure-monitor.md)
- [使用 Azure Data Studio 部署 Azure SQL 托管实例](create-sql-managed-instance-azure-data-studio.md)
