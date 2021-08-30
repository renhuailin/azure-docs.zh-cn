---
title: 使用 Kibana 和 Grafana 查看日志和指标
description: 使用 Kibana 和 Grafana 查看日志和指标
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: d8d18d296a54b85cb3b571099e13f8c3e4e7a9c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750243"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>使用 Kibana 和 Grafana 查看日志和指标

提供了 Kibana 和 Grafana Web 仪表板，可让你深入了解并明确启用了 Azure Arc 的数据服务所使用的 Kubernetes 命名空间。



## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>监视 Azure Arc 上的 Azure SQL 托管实例

若要访问已启用 Arc 的 SQL 托管实例的日志和监视仪表板，请运行以下 `azdata` CLI 命令

```azurecl
az sql mi-arc endpoint list -n <name of SQL instance>

```
相关的 Grafana 仪表板包括：

* “Azure SQL 托管实例指标”
* “主机节点指标”
* “主机 Pod 指标”


> [!NOTE]
>  系统提示输入用户名和密码时，请输入在创建 Azure Arc 数据控制器时提供的用户名和密码。

> [!NOTE]
>  由于证书是自签名证书，因此会提示证书警告。


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>监视 Azure Arc 上的 Azure Database for PostgreSQL 超大规模

若要访问 PostgreSQL 超大规模的日志和监视仪表板，请运行以下 `azdata` CLI 命令

```bash

az postgres arc-server endpoint list -n <name of postgreSQL instance> --k8s-namespace <namespace> --use-k8s

```

相关的 postgreSQL 仪表板包括：

* “Postgres 指标”
* “Postgres 表指标”
* “主机节点指标”
* “主机 Pod 指标”


## <a name="additional-firewall-configuration"></a>其他防火墙配置

根据数据控制器的部署位置，你可能会发现需要在防火墙上打开端口才能访问 Kibana 和 Grafana 终结点。

下面是有关如何为 Azure VM 执行此操作的示例。 如果已使用脚本部署了 Kubernetes，则需要执行此操作。

以下步骤重点介绍如何为 Kibana 和 Grafana 终结点创建 NSG 规则：

### <a name="find-the-name-of-the-nsg"></a>查找 NSG 的名称

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>添加 NSG 规则

获得 NSG 的名称后，可以使用以下命令添加规则：

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>后续步骤
- 尝试[将指标和日志上传到 Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- 阅读了解有关 Grafana 的信息：
   - [入门](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana 基础知识](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana 教程](https://grafana.com/tutorials/grafana-fundamentals/#1)
- 阅读了解有关 Grafana 的信息
   - [介绍](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana 指南](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [在 Kibana 中使用数据可视化效果进行仪表板向下钻取简介](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [如何构建 Kibana 仪表板](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

