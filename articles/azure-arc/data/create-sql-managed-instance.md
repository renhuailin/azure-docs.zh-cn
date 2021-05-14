---
title: 在 Azure Arc 上创建 Azure SQL 托管实例
description: 在 Azure Arc 上创建 Azure SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: rothja
ms.author: jroth
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4c416d58e4c505ec15d5afa1520f19af62ceaf3d
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109738007"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>在 Azure Arc 上创建 Azure SQL 托管实例

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>登录 Azure Arc 数据控制器

如果尚未登录 Azure Arc 数据控制器，请先登录，然后才能创建实例。

```console
azdata login
```

然后系统会提示输入用户名、密码和系统命名空间。  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>创建 Azure SQL 托管实例

如果要查看可用的 SQL 托管实例创建选项，请使用以下命令：
```console
azdata arc sql mi create --help
```

如果要创建 SQL 托管实例，请使用以下命令：

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

示例：

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  名称长度必须少于 13 个字符，且符合 [DNS 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  当指定内存分配和 vCore 分配时，使用此公式来确保创建成功 - 在运行 SQL 托管实例的 Kubernetes 节点上每 1 个 vCore 需要至少有 4 GB 的 RAM 容量。
>
>  创建 SQL 实例时，如果在 Azure 中进行预配，则不要在名称中使用大写字母
>
>  如果要列出 Kubernetes 群集中的可用存储类，运行 `kubectl get storageclass` 


> [!NOTE]
> 如果要自动创建 SQL 实例并避免管理员密码的交互式提示，可以在运行 `azdata arc sql mi create` 命令之前，将 `AZDATA_USERNAME` 和 `AZDATA_PASSWORD` 环境变量设置为所需的用户名和密码。
> 
>  如果在同一终端会话中使用 AZDATA_USERNAME 和 AZDATA_PASSWORD 创建了数据控制器，则 AZDATA_USERNAME 和 AZDATA_PASSWORD 的值也将用于创建 SQL 托管实例。

> [!NOTE]
> 创建 Azure SQL 托管实例不会在 Azure 中注册资源。 以下文章介绍了注册资源的步骤： 
> - [使用 Kibana 和 Grafana 查看日志和指标](monitor-grafana-kibana.md)
> - [将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>在 Azure Arc 上查看实例

如果要查看实例，请使用以下命令：

```console
azdata arc sql mi list
```

输出应如下所示：

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

如果使用的是 AKS 或 `kubeadm` 或 OpenShift 等，则可以从此处复制外部 IP 和端口号，并使用你常用的工具连接到它，以便连接到 SQL server/AZURE SQL 实例，如 Azure Data Studio 或 SQL Server Management Studio。 但是，如果使用的是快速入门 VM，请参阅“[连接到已启用 Azure Arc 的 SQL 托管实例](connect-managed-instance.md)”一文，了解特殊说明。


## <a name="next-steps"></a>后续步骤
- [连接到已启用 Azure Arc 的 SQL 托管实例](connect-managed-instance.md)
- [向 Azure 注册实例并上传有关实例的指标和日志](upload-metrics-and-logs-to-azure-monitor.md)
- [使用 Azure Data Studio 部署 Azure SQL 托管实例](create-sql-managed-instance-azure-data-studio.md)
