---
title: 数据收集和报告 | 已启用 Azure Arc 的数据服务
description: 说明由已启用 Arc 的数据服务向 Microsoft 传输的数据类型。
author: dnethi
ms.author: dinethi
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
ms.date: 07/30/2021
ms.custom: template-concept
ms.openlocfilehash: 189021997362da8508d2e60c23cc3acb1238ca55
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728031"
---
# <a name="azure-arc-data-services-data-collection-and-reporting"></a>Azure Arc 数据服务数据收集和报告

本文介绍已启用 Azure Arc 的数据服务向 Microsoft 传输的数据。 


## <a name="related-products"></a>相关产品

已启用 Azure Arc 的数据服务可能使用以下部分或所有产品：

- SQL MI - Azure Arc 
- 超大规模 PostgreSQL - Azure Arc
- Azure Data Studio

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- Azure CLI (az)
- Azure Data CLI (`azdata`) 

## <a name="directly-connected"></a>直接连接

将群集配置为直接连接到 Azure 时，某些数据会自动传输到 Microsoft。 

下表描述了数据的类型、发送方式和要求。  

|数据类别|发送哪些数据？|如何发送？|是否必需发送？
|:----|:----|:----|:----|
|操作数据|指标和日志|自动（如果配置为这样做）|否
计费和清单数据|清单（如实例数）和使用情况（如使用的 vCore 数）|自动 |是
诊断|用于故障排除的诊断信息|手动导出并提供给 Microsoft 支持部门|仅适用于故障排除范围并遵循标准[隐私政策](https://privacy.microsoft.com/privacystatement)
客户体验改善计划 (CEIP)|[CEIP 摘要](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|自动（如果允许）|否

## <a name="indirectly-connected"></a>间接连接

当群集未配置为直接连接到 Azure 时，它不会自动将操作数据或计费和清单数据传输给 Microsoft。 若要将数据传输到 Microsoft，需要配置导出。 

下表描述了数据的类型、发送方式和要求。  

|数据类别|发送哪些数据？|如何发送？|是否必需发送？
|:----|:----|:----|:----|
|操作数据|指标和日志|手动|否
计费和清单数据|清单（如实例数）和使用情况（如使用的 vCore 数）|手动 |是
诊断|用于故障排除的诊断信息|手动导出并提供给 Microsoft 支持部门|仅适用于故障排除范围并遵循标准[隐私政策](https://privacy.microsoft.com/privacystatement)
客户体验改善计划 (CEIP)|[CEIP 摘要](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|自动（如果允许）|否

## <a name="detailed-description-of-data"></a>数据的详细说明

本部分提供有关已启用 Azure Arc 的数据服务传输到 Microsoft 的数据的更多详细信息。

### <a name="operational-data"></a>运营数据

系统针对所有数据库实例和已启用 Arc 的数据服务平台本身收集操作数据。 有两种类型的操作数据： 

- 指标 - 与性能和容量相关的指标，收集到已启用 Arc 的数据服务中提供的 Influx DB。 可以在提供的 Grafana 仪表板中查看这些指标。 

- 日志 - 所有组件发出的日志（包括故障、警告和信息性事件），收集到已启用 Arc 的数据服务中提供的 Elasticsearch 数据库。 可以在提供的 Kibana 仪表板中查看日志。 

需要内置管理权限才能在 Grafana/Kibana 中查看本地存储的操作数据。 

操作数据不会离开你的环境，除非选择将数据导出/上传（间接连接模式）或自动发送（直接连接模式）到 Azure Monitor/Log Analytics。 数据将进入你控制的 Log Analytics 工作区。 

如果将数据发送到 Azure Monitor 或 Log Analytics，则可以选择 Log Analytics 工作区要位于哪个 Azure 区域或数据中心。 之后你即可控制从其他位置查看或复制该工作区的权限。 

### <a name="billing-and-inventory-data"></a>计费和清单数据 

计费数据用于跟踪可计费的使用情况。 此数据对于运行服务至关重要，需要在所有模式下以手动或自动方式进行传输。 

每个数据库实例和数据控制器本身将作为 Azure 资源管理器中的 Azure 资源反映在 Azure 中。 

有三种资源类型： 

- 已启用 Arc 的 SQL 托管实例 
- 已启用 Arc 的超大规模 PostgreSQL 服务器组 
- 启用了 Azure Arc 的服务器上的 SQL Server 
- 数据控制器 

以下部分显示了针对每种资源类型收集和存储的属性、类型和说明： 

### <a name="sql-server-on-azure-arc-enabled-servers"></a>启用了 Azure Arc 的服务器上的 SQL Server 
- SQL Server 版本。 
   - `string: Edition` 
- 容器资源 (Azure Arc for Servers) 的资源 ID。 
   - `string: ContainerResourceId` 
- 创建资源的时间。 
   - `string: CreateTime` 
- SQL Server 实例使用的逻辑处理器数。
   - `string: VCore` 
- 云连接状态。 
   - `string: Status` 
- SQL Server 更新级别。 
   - `string: PatchLevel` 
- SQL Server 排序规则。 
   - `string: Collation`
- SQL Server 当前版本。
   - `string: CurrentVersion`
- SQL Server 实例名。 
   - `string: InstanceName`
- SQL Server 使用的动态 TCP 端口。 
   - `string: TcpDynamicPorts`
- SQL Server 使用的静态 TCP 端口。
   - `string: TcpStaticPorts` 
- SQL Server 产品 ID。
   - `string: ProductId`
- SQL Server 预配状态。
   - `string: ProvisioningState`

### <a name="data-controller"></a>数据控制器 

- 位置信息
   - `public OnPremiseProperty OnPremiseProperty` 
- 原始 Kubernetes 信息 (`kubectl get datacontroller`) 
   - `object: K8sRaw` 
- 上次从本地群集上传的日期。
   - `System.DateTime: LastUploadedDate` 
- 数据控制器状态
   - `string: ProvisioningState` 

### <a name="postgresql-hyperscale-server-group"></a>超大规模 PostgreSQL 服务器组 

- 数据控制器 ID
   - `string: DataControllerId`
- 实例管理员名称
   - `string: Admin`
- 基本身份验证的用户名和密码
   - `public: BasicLoginInformation BasicLoginInformation` - 原始 Kubernetes 信息 (`kubectl get postgres12`) 
   - `object: K8sRaw` - 上次从本地群集上传的日期。 
   - `System.DateTime: LastUploadedDate` 
- 组预配状态
   - `string: ProvisioningState` 

### <a name="sql-managed-instance"></a>SQL 托管实例 

- 托管实例 ID
   - `public string: DataControllerId` 
- 实例管理员用户名 
   - `string: Admin` 
- 实例开始时间 
   - `string: StartTime`
- 实例结束时间 
   - `string: EndTime` 
- 原始 kubernetes 信息 (`kubectl get sqlmi`) 
   - `object: K8sRaw` 
- 基本身份验证的用户名和密码。 
   - `public: BasicLoginInformation BasicLoginInformation`
- 上次从本地群集上传的日期。 
   - `public: System.DateTime LastUploadedDate` 
- SQL 托管实例预配状态
   - `public string: ProvisioningState` 

### <a name="examples"></a>示例

发送到 Azure 的资源清单数据 JSON 文档的示例，用于在订阅中创建 Azure 资源。 

```json
{ 

        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711", 

        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373", 

        "instanceName": "sqlInstance001", 

        "instanceNamespace": "arc", 

        "instanceType": "<resource>", 

        "location": "eastus", 

        "resourceGroupName": "production-resources", 

        "subscriptionId": "<subscription_id>", 

        "isDeleted": false, 

        "externalEndpoint": "32.191.39.83:1433", 

        "vCores": "2", 

        "createTimestamp": "05/29/2020 23:13:17", 

        "updateTimestamp": "05/29/2020 23:13:17" 

    } 
```

 

计费数据捕获给定实例的开始时间（“创建时间”）和结束时间（“删除时间”），还捕获每当给定实例的可用核心数（“核心限制”）发生变化时的任何开始时间。 

```json
{ 

          "requestType": "usageUpload", 

          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d", 

          "name": "DataControllerTestName", 

          "subscriptionId": "<subscription_id>", 

          "resourceGroup": "production-resources", 

          "location": "eastus", 

          "uploadRequest": { 

            "exportType": "usages", 

            "dataTimestamp": "2020-06-17T22:32:24Z", 

            "data": "[{\"name\":\"sqlInstance001\", 

                       \"namespace\":\"arc\", 

                       \"type\":\"<resource type>\", 

                       \"eventSequence\":1,  

                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\", 

                       \"startTime\":\"2020-06-17T19:11:47.7533333\", 

                       \"endTime\":\"2020-06-17T19:59:00\", 

                       \"quantity\":1, 

                       \"id\":\"<subscription_id>\"}]", 

           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK" 

```

### <a name="diagnostic-data"></a>诊断数据

在寻求支持的情况下，可能需要提供数据库实例日志、Kubernetes 日志和其他诊断日志。 支持团队将提供安全的上传位置。 动态管理视图 (DMV) 也可能提供诊断数据。 使用的 DMV 或查询可能包含数据库架构元数据详细信息，但通常不包含客户数据。 诊断数据不包含任何密码、群集 IP 或个人身份数据。 系统将清理这些信息，并且会尽可能匿名存储日志。 这些内容不会自动传输，管理员必须手动上传。 

|字段名称  |说明  |
|---------|---------|
|错误日志 |捕获错误的日志文件可能包含客户或个人数据（如下所示），这些内容受限制并由用户共享 |
|DMV      |动态管理视图可能包含查询和查询计划，但这些内容受限制并由用户共享     |
|视图    |视图可能包含客户数据，但这些内容受限制并仅可由用户共享     |
|故障转储 - 客户数据 | 故障转储可能包含访问控制数据，最多可保留 30 天 <br/><br/> 客户故障转储中可能包含统计信息对象、行内的数据值和查询文本    |
|故障转储 - 个人数据 | 计算机、登录名/用户名、电子邮件、位置信息、客户标识 - 需要用户同意才可包含这些信息  |

## <a name="next-steps"></a>后续步骤
[将使用情况数据上传到 Azure Monitor](upload-usage-data.md)
