---
title: Azure Monitor for SAP Solutions 提供程序 | Microsoft Docs
description: 本文提供了有关 Azure Monitor for SAP Solutions 提供程序的常见问题解答。
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: fe8ac81a8b04aa88ce91a978c2bc9b979a065370
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576153"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor for SAP Solutions 提供程序（预览版）

## <a name="overview"></a>概述  

在 Azure Monitor for SAP Solutions 环境中，提供程序类型指特定提供程序 。 例如 SAP HANA，它是针对 SAP 环境中的特定组件（如 SAP HANA 数据库）而配置。 提供程序包含相应组件的连接信息，可帮助从该组件收集遥测数据。 一种 Azure Monitor for SAP Solutions 资源（也称为 SAP 监视器资源）可以使用同一提供程序类型的多个提供程序或多种提供程序类型的多个提供程序进行配置。
   
客户可以选择配置不同的提供程序类型，以便可在其 SAP 环境中从相应组件收集数据。 例如，客户可以为 SAP HANA 提供程序类型配置一个提供程序，为高可用性群集提供程序类型配置另一个提供程序，等等。  

客户还可以选择配置特定提供程序类型的多个提供程序，以重复使用相同 SAP 监视器资源和关联托管组。 了解有关托管资源组的详细信息。 对于公共预览版，支持以下提供程序类型：   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- 高可用性群集
- 操作系统

![Azure Monitor for SAP solutions 提供程序](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

建议在部署 SAP 监视器资源时，客户从可用提供程序类型配置至少一个提供程序。 通过配置提供程序，客户可以开始从为其配置提供程序的相应组件收集数据。   

如果客户在部署 SAP 监视器资源时未配置任何提供程序，则尽管 SAP 监视器资源会成功部署，但不会收集任何遥测数据。 客户可以选择部署后在 Azure 门户中通过 SAP 监视器资源添加提供程序。 客户可随时从 SAP 监视器资源添加或删除提供程序。

## <a name="provider-type-sap-netweaver"></a>提供程序类型：SAP NetWeaver

客户可以配置提供程序类型 SAP NetWeaver 的一个或多个提供程序，以便可以从 SAP NetWeaver 层收集数据。 AMS NetWeaver 提供程序利用现有的 [SAPControl webservice](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) 接口检索相应的遥测信息。

对于当前版本，下面是 AMS 调用的标准现成 SOAP web 方法。

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

在公共预览版中，客户预计可以通过 SAP NetWeaver 提供程序看到以下数据： 
- 系统和实例可用性
- 工作进程利用率
- 队列利用率
- 排队锁定统计信息。

![图像](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>提供程序类型：SAP HANA

客户可以配置 SAP HANA 提供程序类型的一个或多个提供程序，以便可以从 SAP HANA 数据库收集数据。 SAP HANA 提供程序通过 SQL 端口连接到 SAP HANA 数据库，从数据库拉取遥测数据，并推送到客户订阅中的 Log Analytics 工作区。 SAP HANA 提供程序每 1 分钟从数据库收集一次 SAP HANA 数据。  

在公共预览版中，客户可以预计通过 SAP HANA 提供程序查看以下数据：底层基础结构利用率、SAP HANA 主机状态、SAP HANA 系统复制和 SAP HANA 备份遥测数据。 若要配置 SAP HANA 提供程序，需要主机 IP 地址、HANA SQL 端口号以及 SYSTEMDB 用户名和密码。 建议客户针对 SYSTEMDB 配置 SAP HANA提供程序，但可以针对其他数据库租户配置更多提供程序。

![Azure Monitor for SAP solutions 提供程序 - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>提供程序类型：Microsoft SQL server

客户可以配置 Microsoft SQL Server 提供程序类型的一个或多个提供程序，以便可以从[虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 收集数据。 SQL Server 提供程序通过 SQL 端口连接到 Microsoft SQL Server，从数据库拉取遥测数据，并推送到客户订阅中的 Log Analytics 工作区。 必须为 SQL 身份验证配置 SQL Server，并且必须创建 SQL Server 登录（将 SAP DB 作为提供程序的默认数据库）。 SQL Server 提供程序按照介于每 60 秒到每小时直接的频率从 SQL Server 收集数据。  

在公共预览版中，客户可以预计通过 SQL Server 提供程序查看以下数据：底层基础结构利用率、顶层 SQL 语句、顶层最大表、在 SQL Server 错误日志中记录的问题、阻塞进程和其他内容。  

若要配置 Microsoft SQL Server 提供程序，需要 SAP 系统 ID、主机 IP 地址、SQL Server 端口号以及 SQL Server 登录名和密码。

![Azure Monitor for SAP solutions 提供程序 - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>提供程序类型：高可用性群集
客户可以配置高可用性群集提供程序类型的一个或多个提供程序，以便可以在 SAP 环境中从 Pacemaker 群集收集数据。 高可用性群集提供程序使用 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 终结点连接到 Pacemaker，从数据库拉取遥测数据，并将数据推送到客户订阅中的 Log Analytics 工作区。 高可用性群集提供程序每 60 秒从 Pacemaker 收集一次数据。  

在公共预览版中，客户可以预计通过高可用性群集提供程序查看以下数据：   
 - 以节点和资源状态的汇总形式表示的群集状态 
 - [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor for SAP solutions 提供程序 - 高可用性群集](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

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
   - 主机名。 VM 的 Linux 主机名。  

## <a name="provider-type-os-linux"></a>提供程序类型：OS (Linux)
客户可以配置 OS (Linux) 提供程序类型的一个或多个提供程序，以便可以从 BareMetal 或 VM 节点收集数据。 OS (Linux) 提供程序使用  [Node_Exporter](https://github.com/prometheus/node_exporter) 终结点连接到 BareMetal 或 VM 节点，从节点拉取遥测数据，并将数据推送到客户订阅中的 Log Analytics 工作区。 OS (Linux) 提供程序每 60 秒从节点收集一次大多数指标的数据。 

在公共预览版中，客户可以预计通过 OS (Linux) 提供程序查看以下数据： 
   - CPU 使用率、按进程表示的 CPU 使用率 
   - 磁盘利用率、I/O 读取和写入 
   - 内存分布、内存使用量、交换内存使用量 
   - 网络使用情况、网络入站和出站流量详细信息。 

若要配置 OS (Linux) 提供程序，涉及执行两个主要步骤：
1. 在每个 BareMetal 或 VM 节点安装 [Node_Exporter](https://github.com/prometheus/node_exporter) 。
   安装 [Node_exporter](https://github.com/prometheus/node_exporter) 有两个选项： 
      - 若要使用 Ansible 进行自动化安装，请在每个 BareMetal 或 VM 节点上使用 [Node_Exporter](https://github.com/prometheus/node_exporter) 安装 OS (Linux) 提供程序。  
      - 执行 [手动安装](https://prometheus.io/docs/guides/node-exporter/)。

2. 为环境中的每个 BareMetal 或 VM 节点实例配置 OS (Linux) 提供程序。 
   若要配置 OS (Linux) 提供程序，需要以下信息： 
      - 名称： 此提供程序的名称。 对于此 Azure Monitor for SAP solutions 实例，它应是唯一的。 
      - 节点导出程序终结点。 通常为 http://<servername or ip address>:9100/metrics 

> [!NOTE]
> 9100 是为 Node_Exporter 终结点公开的端口。

> [!Warning]
> 确保节点导出程序在节点重新启动后保持运行。 

## <a name="next-steps"></a>后续步骤

- 请参阅[加入步骤](./azure-monitor-sap-quickstart.md)，创建第一个适用于 SAP 解决方案资源的 Azure Monitor。
- 是否对 Azure Monitor for SAP Solutions 有疑问？ 请查看[常见问题解答](./azure-monitor-faq.md)部分
