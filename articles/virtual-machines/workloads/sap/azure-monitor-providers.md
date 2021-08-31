---
title: Azure Monitor for SAP Solutions 提供程序 | Microsoft Docs
description: 本文提供了有关 Azure Monitor for SAP Solutions 提供程序的常见问题解答。
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: e6d5ad24176c779055fbde2c2ec6e6506cea3285
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392163"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor for SAP Solutions 提供程序（预览版）

## <a name="overview"></a>概述

本文介绍 Azure Monitor for SAP Solutions 当前可用的各种提供程序。

在 Azure Monitor for SAP Solutions 环境中，提供程序类型指特定提供程序 。 例如 SAP HANA，它是针对 SAP 环境中的特定组件（如 SAP HANA 数据库）而配置的。 提供程序包含相应组件的连接信息，可帮助从该组件收集遥测数据。 一种 Azure Monitor for SAP Solutions 资源（也称为 SAP 监视器资源）可以使用同一提供程序类型的多个提供程序或多种提供程序类型的多个提供程序进行配置。
   
可以选择配置不同类型的提供程序，以便在其 SAP 环境中从相应组件收集数据。 例如，可以为 SAP HANA 提供程序类型配置一个提供程序，为高可用性群集提供程序类型配置另一个提供程序，等等。  

此外，还可以选择配置多个特定提供程序类型的提供程序，以重复使用相同 SAP 监视器资源和关联托管组。 有关受管理资源组的详细信息，请参阅[使用 Azure 门户管理 Azure 资源管理器资源组](../../../azure-resource-manager/management/manage-resource-groups-portal.md)。

对于公共预览版，支持以下提供程序类型：   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- 高可用性群集
- 操作系统 (OS)

![Azure Monitor for SAP solutions 提供程序](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

建议在部署 SAP 监视器资源时，至少根据可用提供程序类型配置一个提供程序。 通过配置提供程序，可以开始从为其配置提供程序的相应组件开始收集数据。   

如果在部署 SAP 监视器资源时未配置任何提供程序，则尽管 SAP 监视器资源会成功部署，也不会收集任何遥测数据。 在部署后，可以通过 Azure 门户中的 SAP 监视器资源来添加提供程序。 可以随时在 SAP 监视器资源中添加或删除提供程序。

## <a name="provider-type-sap-netweaver"></a>提供程序类型：SAP NetWeaver

可以配置一个或多个 SAP NetWeaver 提供程序类型的提供程序，以便从 SAP NetWeaver 层收集数据。 AMS NetWeaver 提供程序利用现有的 [SAPControl Web 服务](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html)接口检索相应的遥测信息。

对于当前版本，下面是 AMS 调用的标准现成 SOAP Web 方法。

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

在公共预览版中，可以通过 SAP NetWeaver 提供程序查看以下数据： 
- 系统和实例可用性
- 工作进程使用情况
- 队列使用情况
- 排队锁定统计信息

![图像](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>提供程序类型：SAP HANA

可以配置一个或多个 SAP HANA 提供程序类型的提供程序，以便从 SAP HANA 数据库收集数据。 SAP HANA 提供程序通过 SQL 端口连接到 SAP HANA 数据库，从数据库拉取遥测数据，并将其推送到订阅中的 Log Analytics 工作区。 SAP HANA 提供程序每 1 分钟从数据库收集一次 SAP HANA 数据。  

在公共预览版中，可以通过 SAP HANA 提供程序查看以下数据：
- 底层基础结构使用情况
- SAP HANA 主机状态
- SAP HANA 系统复制
- SAP HANA 备份遥测数据。 

配置 SAP HANA 提供程序需要：
- 主机 IP 地址； 
- HANA SQL 端口号
- SYSTEMDB 用户名和密码

建议针对 SYSTEMDB 配置 SAP HANA 提供程序，但可以针对其他数据库租户配置更多提供程序。

![Azure Monitor for SAP solutions 提供程序 - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>提供程序类型：Microsoft SQL server

可以配置一个或多个 Microsoft SQL Server 提供程序类型的提供程序，以便从[虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 收集数据。 SQL Server 提供程序通过 SQL 端口连接到 Microsoft SQL Server。 然后从数据库中提取遥测数据，并将其推送到订阅中的 Log Analytics 工作区。 配置 SQL Server 以执行 SQL 身份验证，并通过 SQL Server 用户名和密码进行登录。 将 SAP 数据库设置为提供程序的默认数据库。 SQL Server 提供程序按照每 60 秒到每小时的频率从 SQL Server 收集数据。  

在公共预览版中，可以通过 SQL Server 提供程序查看以下数据：
- 底层基础结构使用情况
- Top SQL 语句
- Top 最大表格
- SQL Server 错误日志中记录的问题
- 阻塞进程和其他  

配置 Microsoft SQL Server 提供程序需要：
- SAP 系统 ID
- 主机 IP 地址
- SQL Server 端口号
- SQL Server 用户名和密码

![Azure Monitor for SAP solutions 提供程序 - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>提供程序类型：高可用性群集
可以配置一个或多个高可用性群集提供程序类型的提供程序，以便在 SAP 环境中从 Pacemaker 群集收集数据。 对于基于 SUSE 的群集，高可用性群集提供程序使用 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 连接到 Pacemaker；对于基于 RHEL 的群集，高可用性群集提供程序使用 [Performance co-pilot](https://access.redhat.com/articles/6139852) 连接到 Pacemaker 。 然后，AMS 从数据库中提取遥测数据，并将其推送到订阅中的 Log Analytics 工作区。 高可用性群集提供程序每 60 秒从 Pacemaker 收集一次数据。  

在公共预览版中，可以通过高可用性群集提供程序查看以下数据：   
 - 以节点和资源状态的汇总形式表示的群集状态 
 - 位置约束
 - 趋势
 - [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor for SAP Solutions 提供程序 - 高可用性群集](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

若要配置高可用性群集提供程序，涉及执行两个主要步骤：

1. 在 Pacemaker 群集中的每个节点上安装 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)。

   安装 ha_cluster_exporter 有两个选项：
   
   - 使用 Azure 自动化脚本部署高可用性群集。 这些脚本会在每个群集节点上安装 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)。  
   - 执行[手动安装](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build)。 

2. 为 Pacemaker 群集中的每个节点配置高可用性群集提供程序。

   若要配置高可用性群集提供程序，需要以下信息：
   
   - **名称**。 此提供程序的名称。 对于此 Azure Monitor for SAP solutions 实例，它应是唯一的。
   - Prometheus 终结点。 http\://\<servername or ip address\>:9664/metrics。
   - SID。 对于 SAP 系统，请使用 SAP SID。 对于其他系统（例如 NFS 群集），请使用群集的三字符名称。 SID 必须与受监视的其他群集不同。   
   - 群集名称。 创建群集时使用的群集名称。 群集名称可在群集属性 `cluster-name` 中找到。
   - 主机名。 虚拟机 (VM) 的 Linux 主机名。  

## <a name="provider-type-os-linux"></a>提供程序类型：OS (Linux)
可以配置一个或多个 OS (Linux) 提供程序类型的提供程序，以便从 BareMetal 或 VM 节点收集数据。 OS (Linux) 提供程序通过 [Node_Exporter](https://github.com/prometheus/node_exporter)  终结点连接 BareMetal 或 VM 节点。 然后，它将从节点中提取遥测数据，并将其推送到订阅中的 Log Analytics 工作区。 对于大多数指标，OS (Linux) 提供程序每 60 秒从节点收集一次数据。 

在公共预览版中，可以通过 OS (Linux) 提供程序查看以下数据： 
   - CPU 使用情况，按进程表示的 CPU 使用情况 
   - 磁盘使用情况，I/O 读取和写入 
   - 内存分布、内存使用情况、交换内存使用情况 
   - 网络使用情况、网络入站和出站流量详细信息 

若要配置 OS (Linux) 提供程序，涉及执行两个主要步骤：
1. 在每个 BareMetal 或 VM 节点上安装  [Node_Exporter](https://github.com/prometheus/node_exporter) 。
   安装 [Node_exporter](https://github.com/prometheus/node_exporter) 有两个选项： 
      - 若要使用 Ansible 进行自动化安装，请在每个 BareMetal 或 VM 节点上使用 [Node_Exporter](https://github.com/prometheus/node_exporter) 安装 OS (Linux) 提供程序。  
      - 执行 [手动安装](https://prometheus.io/docs/guides/node-exporter/)。

2. 为环境中的每个 BareMetal 或 VM 节点实例配置一个 OS (Linux) 提供程序。 
   若要配置 OS (Linux) 提供程序，需要以下信息： 
      - 名称： 此提供程序的名称。 对于此 Azure Monitor for SAP Solutions 实例，它应是唯一的。 
      - 节点导出程序终结点。 通常为 http://<servername or ip address>:9100/metrics。 

> [!NOTE]
> 9100 是为 Node_Exporter 终结点公开的端口。

> [!Warning]
> 确保节点导出程序在节点重新启动后保持运行。 

## <a name="next-steps"></a>后续步骤

了解如何从 Azure 门户中部署 Azure Monitor for SAP Solutions。

> [!div class="nextstepaction"]
> [使用 Azure 门户部署 Azure Monitor for SAP Solutions](./azure-monitor-sap-quickstart.md)
