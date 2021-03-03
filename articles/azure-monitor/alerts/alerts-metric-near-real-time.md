---
title: Azure Monitor 中的指标警报支持的资源
description: Azure Monitor 中的指标警报的支持指标和日志的参考
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: b3aa68be050b1ea5c1c32253f1ae6e6353ee8dc7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717904"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor 中的指标警报支持的资源

Azure Monitor 现在支持[新型指标警报类型](./alerts-overview.md)，它比旧式[经典指标警报](./alerts-classic.overview.md)具有显著的优势。 指标可用于 [Azure 服务的大型列表](../essentials/metrics-supported.md)。 新型警报支持资源类型的一个（不断增长的）子集。 本文列出了该子集。

还可以对提取为指标的常用日志数据（在 Log Analytics 工作区中存储）使用新型指标警报。 有关详细信息，请查看[日志的指标警报](./alerts-metric-logs.md)。

## <a name="portal-powershell-cli-rest-support"></a>门户、PowerShell、CLI、REST 支持
目前，仅可在 Azure 门户、[REST API](/rest/api/monitor/metricalerts/) 或[资源管理器模板](./alerts-metric-create-templates.md)中创建新型指标警报。 对于使用 PowerShell 和 Azure CLI 2.0 及更高版本配置新型警报的支持即将推出。

## <a name="metrics-and-dimensions-supported"></a>指标和维度支持
新型指标警报支持针对使用维度的指标发出警报。 可以使用维度将指标筛选到适当级别。 所有受支持的指标以及适用的维度都可以从 [Azure Monitor - 指标资源管理器](../essentials/metrics-charts.md)中进行浏览和可视化。

下面是新型警报支持的 Azure Monitor 指标源的完整列表：

|资源类型  |支持维度 |多资源警报| 可用指标|
|---------|---------|-----|----------|
|Microsoft.Aadiam/azureADMetrics | 是 | 否 | |
|Microsoft.ApiManagement/service | 是 | 否 | [API 管理](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |是 | 否 | [应用配置](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | 是 | 否 | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | 是| 否 | [自动化帐户](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.AVS/privateClouds | 否 | 否 | [Azure VMware 解决方案](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | 是 | 否 | [Batch 帐户](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | 是 | 是 | [用于 Redis 的 Azure 缓存](../essentials/metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | 否 | 否 | [经典云服务](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | 否 | 否 | [经典虚拟机](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | 是 | 否 | [存储帐户（经典）](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | 是 | 否 | [存储帐户（经典）- Blob](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | 是 | 否 | [存储帐户（经典）- 文件](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | 是 | 否 | [存储帐户（经典）- 队列](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | 是 | 否 | [存储帐户（经典）- 表](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | 是 | 否 | [认知服务](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | 是 | 是<sup>1</sup> | [虚拟机](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | 是 | 否 |[虚拟机规模集](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | 是| 否 | [容器组](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | 否 | 否 | [容器注册表](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | 是 | 否 | [托管群集](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | 是 | 是 | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| 是| 否 | [数据工厂 V1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |是 | 否 | [数据工厂 V2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | 是 | 否 | [数据共享](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | 否 | 否 | [DB for MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | 否 | 否 |[适用于 MySQL 的 DB](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | 否 | 否 | [适用于 PostgreSQL 的 DB](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | 否 | 否 | [DB for PostgreSQL V2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | 是 | 否 | [DB for PostgreSQL（灵活服务器）](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | 是 | 否 |[IoT 中心](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| 是 | 否 | [设备预配服务](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DigitalTwins/digitalTwinsInstances | 是 | 否 | [数字孪生](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | 是 | 否 | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | 是 | 否 | [事件网格域](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | 是 | 否 | [事件网格系统主题](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |是 | 否 | [事件网格主题](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |是| 否 | [事件中心群集](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |是| 否 | [事件中心](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | 是 | 否 | [HDInsight 群集](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | 是 | 否 | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | 是 |是 |[保管库](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | 是 |否 |[数据资源管理器群集](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | 是 | 否 |[集成服务环境](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | 否 | 否 |[逻辑应用](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | 是 | 否 | [机器学习](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | 是 | 否 | [Maps 帐户](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | 否 | 否 | [媒体服务](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | 是 | 否 | [媒体服务流式处理终结点](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | 是 | 是 | [Azure NetApp 容量池](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | 是 | 是 | [Azure NetApp 卷](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | 是 | 否 | [应用程序网关](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | 是 | 否 | [防火墙](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | 否 | 否 | [DNS 区域](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 是 | 否 |[ExpressRoute 线路](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | 是 | 否 |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers（仅限标准 SKU）| 是| 否 | [负载均衡器](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| 否 | 否 | [NAT 网关](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft.Network/privateEndpoints| 否 | 否 | [终结点](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| 否 | 否 | [专用链接服务](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses | 否 | 否 | [公共 IP 地址](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | 是 | 否 | [流量管理器配置文件](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| 是 | 否 | [Log Analytics 工作区](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | 是 | 否 | [对等互连](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | 是 | 否 | [对等互连服务](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | 否 | 否 | [容量](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | 是 | 否 | [中继](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | 否 | 否 | [搜索服务](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | 是 | 否 | [服务总线](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | 否 | 是 | [SQL 托管实例](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | 否 | 是 | [SQL 数据库](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | 否 | 是 | [SQL 弹性池](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |是 | 否 | [存储帐户](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | 是| 否 | [存储帐户 - Blob](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | 是| 否 | [存储帐户 - 文件](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | 是| 否 | [存储帐户 - 队列](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | 是| 否 | [存储帐户 - 表](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | 是 | 否 | [HPC 缓存](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft.StorageSync/storageSyncServices | 是 | 否 | [存储同步服务](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | 是 | 否 | [流分析](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | 是 | 否 | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft.Synapse/workspaces/bigDataPools | 是 | 否 | [Synapse Analytics Apache Spark 池](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft.Synapse/workspaces/sqlPools | 是 | 否 | [Synapse Analytics SQL 池](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft.VMWareCloudSimple/virtualMachines | 是 | 否 | [CloudSimple 虚拟机](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | 是 | 否 | [应用服务环境多角色池](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | 是 | 否 | [应用服务环境工作线程池](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | 是 | 否 | [应用服务计划](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | 是 | 否 | [应用程序服务和 Functions](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | 是 | 否 | [应用服务槽](../essentials/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> 不支持虚拟机网络指标（总网络流入量、总网络流出量、入站流数、出站流数、入站流最大创建速率、出站流最大创建速率）和自定义指标。

## <a name="payload-schema"></a>负载架构

> [!NOTE]
> 还可以使用[常见警报架构](./alerts-common-schema.md)，它的优点是可以跨 Azure Monitor 中的所有警报服务提供单个可扩展且统一的警报有效负载，用于 Webhook 集成。 [了解常见的警报架构定义。](./alerts-common-schema-definitions.md)


使用适当配置的[操作组](./action-groups.md)时，POST 操作对于所有准新型指标警报包含以下 JSON 有效负载和架构：

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

* 详细了解新式[警报体验](./alerts-overview.md)。
* 了解 [Azure 中的日志警报](./alerts-unified-log.md)。
* 了解 [Azure 中的警报](./alerts-overview.md)。