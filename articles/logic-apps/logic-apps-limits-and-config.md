---
title: 限制和配置参考指南
description: Azure 逻辑应用的限制和配置信息的参考指南
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: 8a43fa4f3516890b4e4eac63d3714fd39bb65225
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252896"
---
# <a name="limits-and-configuration-reference-for-azure-logic-apps"></a>Azure 逻辑应用的限制和配置参考

> 对于 Power Automate，请参阅 [Power Automate 中的限制和配置](/flow/limits-and-config)。

本文介绍 Azure 逻辑应用和相关资源的限制和配置信息。 若要创建逻辑应用工作流，请根据你的情况、解决方案要求、所需功能以及要在其中运行工作流的环境，选择逻辑应用资源类型。

> [!NOTE]
> 这些主机环境中的许多限制都是相同的，但请注意，它们存在的位置有所不同。 如果你的情况需要其他限制，请[与逻辑应用团队联系](mailto://logicappspm@microsoft.com)以讨论你的要求。

下表简要概述了原始逻辑应用（消耗版）资源类型和逻辑应用（标准版）资源类型之间的差异 。 你还将了解单租户环境与多租户和集成服务环境 (ISE) 在部署、托管和运行逻辑应用工作流方面的比较情况  。

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## <a name="workflow-definition-limits"></a>工作流定义限制

下表列出了单个工作流定义的值：

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| 每个订阅每个区域的工作流数 | 1,000 个工作流 ||
| 每个工作流的触发数 | 10 个触发器 | 此限制仅在代码视图或 Azure 资源管理器 (ARM) 模板中使用 JSON 工作流定义时适用，在设计器中不适用。 |
| 每个工作流的操作数 | 500 个操作 | 若要扩展此限制，可根据需要使用嵌套工作流。 |
| 操作嵌套深度 | 8 个操作 | 若要扩展此限制，可根据需要使用嵌套工作流。 |
| 触发器或操作 - 最大名称长度 | 80 个字符 ||
| 触发器或操作 - 最大输入或输出大小 | 104,857,600 字节 <br>(105 MB) | 有关在单租户模型中编辑默认限制的详细信息，请查看[在单租户 Azure 逻辑应用中编辑逻辑应用的主机和应用设置](edit-app-settings-host-settings.md)。 |
| 操作 - 最大组合输入和输出大小 | 209,715,200 字节 <br>(210 MB) ||
| 表达式字符限制 | 8,192 个字符 ||
| `description` - 最大长度 | 256 个字符 ||
| `parameters` - 最大项数 | 50 个参数 ||
| `outputs` - 最大项数 | 10 个输出 ||
| `trackedProperties` - 最大大小 | 8,000 个字符 ||
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>运行持续时间和保留期历史记录限制

下表列出了单个工作流运行的值：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 存储中的运行历史记录保留期 | 90 天 | 90 天 | 366 天 | 运行开始后在存储中保留工作流运行历史记录的时间长度。 <p>注意：如果工作流的运行持续时间超过保留期限制，则会从存储内的运行历史记录中删除该运行。 如果在达到保留期限制后未立即删除某个运行，则会在 7 天内删除该运行。 <p><p>不管某个运行是已完成还是已超时，始终会使用该运行的开始时间以及在工作流设置[运行历史记录保留期(天)](#change-retention)中指定的当前限制来计算运行历史记录保留期。 无论先前的限制如何，将始终使用当前限制来计算保留期。 有关详细信息，请查看[更改存储中的持续时间和运行历史记录保留期](#change-retention)。 <p><p>**提示**：对于需要其他限制的情况，[请与逻辑应用团队联系](mailto://logicappspm@microsoft.com)以讨论你的要求。 |
| 运行持续时间 | 90 天 | - 有状态工作流：90 天 <p><p>- 无状态工作流：5 分钟 | 366 天 | 在强制执行超时之前工作流可以继续运行的时间长度。 <p>运行持续时间是按照运行开始时间以及在开始时工作流设置“[运行历史记录保留期(天)](#change-duration)”中指定的限制计算的。 <p>**重要说明**：请确保运行持续时间值始终小于或等于存储值中的运行历史记录保留期。 否则，运行历史记录可能会在关联的作业完成之前被删除。 <p><p>有关详细信息，请查看[更改存储中的运行持续时间和历史记录保持期](#change-duration)。 <p><p>**提示**：对于需要其他限制的情况，[请与逻辑应用团队联系](mailto://logicappspm@microsoft.com)以讨论你的要求。 |
| 重复间隔 | - 最小值：1秒 <p><p>- 最大值：500 天 | - 最小值：1秒 <p><p>- 最大值：500 天  | - 最小值：1秒 <p><p>- 最大值：500 天 ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>更改存储中的运行持续时间和历史记录保留期

在设计器中，同一设置控制工作流可以运行的最大天数，并控制在存储中保留运行历史记录的最大天数。

* 对于多租户服务，90 天的默认限制就是上限。 只能减小此值。

* 对于单租户服务，可调高或调低 90 天的默认限制。 有关详细信息，请参阅[使用 Visual Studio Code 为单租户 Azure 逻辑应用创建工作流](create-single-tenant-workflows-visual-studio-code.md)。

* 对于集成服务环境，可调高或调低 90 天的默认限制。

> [!TIP]
> 对于需要其他限制的情况，[请与逻辑应用团队联系](mailto://logicappspm@microsoft.com)以讨论你的要求。

例如，假设你将保留期限制从 90 天减少到 30 天。 将从运行历史记录中删除 60 天的运行。 如果将保持期从 30 天增至 60 天，则已经保留了 20 天的运行将在运行历史记录中继续保留 40 天。

> [!IMPORTANT]
> 如果运行的持续时间超过当前运行历史记录保留期限制，将从存储的运行历史记录中删除该运行。 若要避免丢失运行历史记录，请确保保留期限制始终大于运行的最长持续时间。

若要更改这些属性的默认值或当前限制，请执行以下步骤：

#### <a name="portal-multi-tenant-service"></a>[门户（多租户服务）](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)搜索框中，找到并选择“逻辑应用”。

1. 在逻辑应用设计器中找到并打开你的逻辑应用。

1. 在逻辑应用的菜单中选择“工作流设置”。

1. 在“运行时选项”下，从“运行历史记录保留天数”列表中选择“自定义”  。

1. 拖动滑块更改所需的天数。

1. 完成后，在“工作流设置”工具栏上选择“保存”。 

#### <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

如果使用 Azure 资源管理器模板，此设置将显示为工作流资源定义中的属性，如 [Microsoft.Logic 工作流模板参考](/azure/templates/microsoft.logic/workflows)中所述：

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## <a name="looping-concurrency-and-debatching-limits"></a>循环、并发和取消批处理的限制

下表列出了单个工作流运行的值：

### <a name="loop-actions"></a>循环操作

<a name="for-each-loop"></a>

#### <a name="for-each-loop"></a>For each 循环

下表列出了 For each 循环的值：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 数组项数 | 100,000 项 | - 有状态工作流：100,000 项 <p><p>- 无状态工作流：100 项 | 100,000 项 | For each 循环可处理的数组项数。 <p><p>可以使用[查询操作](logic-apps-perform-data-operations.md#filter-array-action)筛选更大数组。 |
| 并发迭代数 | 并发关闭：20 <p><p>并发启用： <p>- 默认值：20 <br>- 最小值：1 <br>- 最大值：50 | 并发关闭：20 <p><p>并发启用： <p><p>- 默认值：20 <br>- 最小值：1 <br>- 最大值：50 | 并发关闭：20 <p><p>并发启用： <p>- 默认值：20 <br>- 最小值：1 <br>- 最大值：50 | 可同时或并行运行的 For each 循环迭代数。 <p><p>若要在多租户服务中更改此值，请参阅[更改 For each 并发限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)或[按顺序运行 For each 循环](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each) 。 |
||||||

<a name="until-loop"></a>

#### <a name="until-loop"></a>Until 循环

下表列出了 Until 循环的值：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 迭代 | - 默认值：60 <br>- 最小值：1 <br>- 最大值：5,000 | 有状态工作流： <p><p>- 默认值：60 <br>- 最小值：1 <br>- 最大值：5,000 <p><p>无状态工作流： <p><p>- 默认值：60 <br>- 最小值：1 <br>- 最大值：100 | - 默认值：60 <br>- 最小值：1 <br>- 最大值：5,000 | 工作流运行期间 Until 循环可以具有的循环周期数。 <p><p>若要更改此值，请在 Until 循环形状中选择“更改限制”，并指定 Count 属性的值  。 |
| 超时 | 默认值：PT1H（1 小时） | 有状态工作流：PT1H（1 小时） <p><p>无状态工作流：PT5M（5 分钟） | 默认值：PT1H（1 小时） | Until 循环在退出前可以运行的时间长度，以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)指定。 将针对每个循环周期评估超时值。 如果循环中的任何操作花费的时间超过超时限制，当前循环便不会停止。 但是，由于不满足限制条件，因此下一个循环不会启动。 <p><p>若要更改此值，请在 Until 循环形状中选择“更改限制”，并指定 Timeout 属性的值  。 |
||||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>并发和取消批处理

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 触发器 - 并发运行数 | 并发关闭：无限制 <p><p>并发启用（不可逆）： <p><p>- 默认值：25 <br>- 最小值：1 <br>- 最大值：100 | 并发关闭：无限制 <p><p>并发启用（不可逆）： <p><p>- 默认值：25 <br>- 最小值：1 <br>- 最大值：100 | 并发关闭：无限制 <p><p>并发启用（不可逆）： <p><p>- 默认值：25 <br>- 最小值：1 <br>- 最大值：100 | 触发器可以同时或并行启动的并发运行数。 <p><p>**注意**：并发启用后，[解除数组批处理](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)的 SplitOn 限制会降低到 100 项。 <p><p>若要在多租户服务中更改此值，请参阅[更改触发器并发限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)或[按顺序触发实例](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger)。 |
| 最大等待运行数 | 并发关闭： <p><p>- 最小值：1 个运行 <p>- 最大值：50 个运行 <p><p>并发启用： <p><p>- 最小值：10 个运行加上并发运行数 <p>- 最大值：100 个运行 | 并发关闭： <p><p>- 最小值：1 个运行 <p>- 最大值：50 个运行 <p><p>并发启用： <p><p>- 最小值：10 个运行加上并发运行数 <p>- 最大值：100 个运行 | 并发关闭： <p><p>- 最小值：1 个运行 <p>- 最大值：50 个运行 <p><p>并发启用： <p><p>- 最小值：10 个运行加上并发运行数 <p>- 最大值：100 个运行 | 在当前工作流实例已在运行最大并发实例数时可以等待运行的工作流实例数。 <p><p>若要在多租户服务中更改此值，请参阅[更改等待运行数限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)。 |
| **SplitOn** 项数 | 并发关闭：100,000 项 <p><p>并发启用：100 项 | 并发关闭：100,000 项 <p><p>并发启用：100 项 | 并发关闭：100,000 项 <p><p>并发启用：100 项 | 对于返回数组的触发器，可指定一个表达式，它使用[将数组项拆分或解除批处理为多个工作流实例](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)进行处理的 SplitOn 属性，而不是使用 For each 循环 。 此表达式引用要用于为每个数组项创建和运行工作流实例的数组。 <p><p>**注意**：并发启用后，SplitOn 限制会降低到 100 项。 |
||||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>吞吐量限制

下表列出了单个工作流定义的值：

| 名称 | 多租户 | 单租户 | 注释 |
|------|--------------|---------------|-------|
| 操作 - 每 5 分钟滚动时间间隔的执行次数 | 默认设置：100,000 次执行 <br>- 高吞吐量模式：300,000 次执行 | 无 | 可以将此默认值提高到工作流的最大值。 有关详细信息，请参阅[在高吞吐量模式下运行](#run-high-throughput-mode)（预览版）。 或者，你可根据需要[在多个工作流之间分配工作负载](handle-throttling-problems-429-errors.md#logic-app-throttling)。 |
| 操作 - 并发出站调用数 | 约 2,500 次调用 | 无 | 你可减少并发请求数，或根据需要减少持续时间。 |
| 托管连接器限制 | 限制因连接器而异 | 限制因连接器而异 | 对于多租户，请查看[每个托管连接器的技术参考页](/connectors/connector-reference/connector-reference-logicapps-connectors)。 <p><p>有关处理连接器限制的详细信息，请参阅[处理限制问题（“429 - 请求过多”错误）](handle-throttling-problems-429-errors.md#connector-throttling)。 |
| 运行时终结点 - 并发入站调用数 | 约 1,000 次调用 | 无 | 你可减少并发请求数，或根据需要减少持续时间。 |
| 运行时终结点 - 每 5 分钟的读取调用数  | 60,000 次读取调用 | 无 | 此限制适用于从工作流的运行历史记录获取原始输入和输出的调用。 你可根据需要在多个工作流之间分配工作负载。 |
| 运行时终结点 - 每 5 分钟调用的调用数 | 45,000 次调用的调用 | 无 | 可根据需要在多个工作流中分配工作负载。 |
| 每 5 分钟的内容吞吐量 | 600 MB | 无 | 可根据需要在多个工作流中分配工作负载。 |
|||||

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>在高吞吐量模式下运行

对于单个工作流定义，每 5 分钟运行的操作数具有[默认限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 若要将此默认值提高到工作流的[最大值](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)（默认值的三倍），可以启用高吞吐量模式（目前处于预览阶段）。 或者，你可根据需要[在多个工作流之间分配工作负载](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling)。

#### <a name="portal-multi-tenant-service"></a>[门户（多租户服务）](#tab/azure-portal)

1. 在 Azure 门户的逻辑应用菜单中，选择“设置”下的“工作流设置” 。

1. 在“运行时选项” > “高吞吐量”下，将设置更改为“启用”  。

   ![显示 Azure 门户中的逻辑应用菜单的屏幕截图，其中“工作流设置”和“高吞吐量”设置为“启用”。](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

若要在用于部署逻辑应用的 ARM 模板中启用此设置，请在逻辑应用的资源定义的 `properties` 对象中，添加 `runtimeConfiguration` 对象，并将 `operationOptions` 属性设置为 `OptimizedForHighThroughput`：

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

有关逻辑应用资源定义的详细信息，请参阅[概述：使用 Azure 资源管理器模板将 Azure 逻辑应用部署自动化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)。

---

### <a name="integration-service-environment-ise"></a>集成服务环境 (ISE)

* [开发人员 ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)：每分钟最多提供 500 次执行，但请注意以下事项：

  * 确保将此 SKU 仅用于探索、试验、开发或测试，而不用于生产和性能测试。 此 SKU 在回收期间没有服务级别协议 (SLA)、纵向扩展功能或冗余，这意味着你可能会遇到延迟或停机。

  * 后端更新可能会间歇性地中断服务。

* [高级 ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)：下表描述了此 SKU 的吞吐量限制，但若要在正常处理中超过这些限制，或要运行可能超过这些限制的负载测试，请[与逻辑应用团队联系](mailto://logicappsemail@microsoft.com)，获取满足要求的帮助。

  | 名称 | 限制 | 说明 |
  |------|-------|-------|
  | 基础单位执行限制 | 当基础结构容量达到 80% 时，系统受到限制 | 每分钟提供约 4,000 次操作执行，每月大约 1.6 亿次操作执行 |
  | 缩放单元执行限制 | 当基础结构容量达到 80% 时，系统受到限制 | 每个缩放单元每分钟可提供约 2,000 次额外的操作执行，每月约 8000 万次额外的操作执行 |
  | 可添加的缩放单元数上限 | 10 个缩放单元 | |
  ||||

<a name="gateway-limits"></a>

## <a name="data-gateway-limits"></a>数据网关限制

Azure 逻辑应用支持通过本地数据网关执行写入操作（包括插入和更新）。 但是，这些操作存在[有效负载大小限制](/data-integration/gateway/service-gateway-onprem#considerations)。

<a name="variables-action-limits"></a>

## <a name="variables-action-limits"></a>变量操作限制

下表列出了单个工作流定义的值：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 每个工作流的变量数 | 250 个变量 | 250 个变量 | 250 个变量 ||
| 变量 - 最大内容大小 | 104,857,600 个字符 | 有状态工作流：104,857,600 个字符 <p><p>无状态工作流：1,024 个字符 | 104,857,600 个字符 ||
| 变量（数组类型）- 最大数组项数 | 100,000 项 | 100,000 项 | 高级 SKU：100,000 项 <p><p>开发人员 SKU：5,000 项 ||
||||||

<a name="http-limits"></a>

## <a name="http-request-limits"></a>HTTP 请求限制

下表列出了单个入站或出站调用的值：

<a name="http-timeout-limits"></a>

### <a name="timeout-duration"></a>超时持续时间

默认情况下，HTTP 操作和 APIConnection 操作遵循[标准异步操作模式](/azure/architecture/patterns/async-request-reply)，而响应操作则遵循“同步操作模式”。 某些托管连接器操作会进行异步调用或侦听 Webhook 请求，因此这些操作的超时时间可能会长于以下限制。 有关详细信息，请查看[每个连接器的技术参考页](/connectors/connector-reference/connector-reference-logicapps-connectors)，以及[工作流触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)文档。

> [!NOTE]
> 对于单租户模型中的逻辑应用（标准版）资源类型，无状态工作流只能同步运行。

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 出站请求 | 120 秒 <br>（2 分钟） | 230 秒 <br>（3.9 分钟） | 240 秒 <br>（4 分钟） | 出站请求的示例包括 HTTP 触发器或操作进行的调用。 <p><p>**提示**：对于运行时间较长的操作，请使用 [异步轮询模式](../logic-apps/logic-apps-create-api-app.md#async-pattern)或 [“Until”循环](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)。 在调用其他具有[可调用终结点](logic-apps-http-endpoint.md)的工作流时，若要绕过超时限制，可改用内置的 Azure 逻辑应用操作（可在“内置”下的设计器的操作选取器中找到）。 |
| 入站请求 | 120 秒 <br>（2 分钟） | 230 秒 <br>（3.9 分钟） | 240 秒 <br>（4 分钟） | 入站请求的示例包括请求触发器、HTTP Webhook 触发器和 HTTP Webhook 操作收到的调用。 <p><p>**注意**：要使原始调用方能够获得响应，必须在限制内完成响应的所有步骤，除非调用其他嵌套工作流。 有关详细信息，请参阅[调用、触发器或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。 |
||||||

<a name="message-size-limits"></a>

### <a name="messages"></a>消息

| 名称 | 已启用分块 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| 下载内容 - 最大请求数 | 是 | 1,000 个请求 | 1,000 个请求 | 1,000 个请求 ||
| 消息大小 | 否 | 100 MB | 100 MB | 200 MB | 若要解决此限制问题，请参阅[使用分块处理大型消息](../logic-apps/logic-apps-handle-large-messages.md)。 但是，某些连接器和 API 不支持分块，甚至不支持默认限制。 <p><p>- 连接器（如 AS2、X12 和 EDIFACT）具有自己的 [B2B 消息限制](#b2b-protocol-limits)。 <p>- ISE 连接器使用 ISE 限制，而不是非 ISE 连接器限制。 |
| 消息大小 | 是 | 1GB | 1,073,741,824 字节 <br>(1 GB) | 5 GB | 此限制适用于本机支持分块或可在其运行时配置中启用分块的操作。 <p><p>如果你使用的是 ISE，则逻辑应用引擎支持此限制，但连接器具有自己的分块限制（不超过引擎限制），例如请参阅 [Azure Blob 存储连接器的 API 参考](/connectors/azureblob/)。 有关分块的详细信息，请参阅[使用分块处理大型消息](../logic-apps/logic-apps-handle-large-messages.md)。 |
| 内容区块大小 | 是 | 因连接器而异 | 52,428,800 字节 (52 MB) | 因连接器而异 | 此限制适用于本机支持分块或可在其运行时配置中启用分块的操作。 |
|||||||

### <a name="character-limits"></a>字符限制

| 名称 | 限制 | 说明 |
|------|-------|-------|
| 表达式计算限制 | 131,072 个字符 | `@concat()`、`@base64()`、`@string()` 表达式的长度不能超过此限制。 |
| 请求 URL 字符限制 | 16,384 个字符 | |
||||

<a name="retry-policy-limits"></a>

### <a name="retry-policy"></a>重试策略

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| 重试次数 | - 默认值：4 次尝试 <br> - 最大值：90 次尝试 | 若要更改默认值，请使用[重试策略参数](../logic-apps/logic-apps-workflow-actions-triggers.md)。 |
| 重试最大延迟 | - 默认值：1 天 | 若要更改默认值，请使用[重试策略参数](../logic-apps/logic-apps-workflow-actions-triggers.md)。 |
| 重试最小延迟 | - 默认值：5 秒 | 若要更改默认值，请使用[重试策略参数](../logic-apps/logic-apps-workflow-actions-triggers.md)。 |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>身份验证限制

下表列出了某个工作流的值，该工作流先是使用请求触发器，并启用 [Azure Active Directory 开放式身份验证](../active-directory/develop/index.yml) (Azure AD OAuth) 来授权对请求触发器的入站调用：

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| Azure AD 授权策略 | 5 个策略 | |
| 每个授权策略的声明 | 10 个声明 | |
| 声明值 - 最大字符数 | 150 个字符 |
||||

<a name="switch-action-limits"></a>

## <a name="switch-action-limits"></a>切换操作限制

下表列出了单个工作流定义的值：

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| 每项操作的最大案例数 | 25 ||
||||

<a name="inline-code-action-limits"></a>

## <a name="inline-code-action-limits"></a>内联代码操作限制

下表列出了单个工作流定义的值：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 最大代码字符数 | 1,024 个字符 | 100,000 个字符 | 1,024 个字符 | 若要使用更高的限制，请使用 [Azure 门户](create-single-tenant-workflows-azure-portal.md)或[使用 Visual Studio Code 和 Azure 逻辑应用（标准版）扩展](create-single-tenant-workflows-visual-studio-code.md)创建逻辑应用（标准版）资源，该资源在单租户 Azure 逻辑应用中运行 。 |
| 运行代码的最长持续时间 | 5 秒 | 15 秒 | 1,024 个字符 | 若要使用更高的限制，请使用 [Azure 门户](create-single-tenant-workflows-azure-portal.md)或[使用 Visual Studio Code 和 Azure 逻辑应用（标准版）扩展](create-single-tenant-workflows-visual-studio-code.md)创建逻辑应用（标准版）资源，该资源在单租户 Azure 逻辑应用中运行 。 |
||||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>自定义连接器限制

仅在多租户和集成服务环境中，可以创建和使用[自定义托管连接器](/connectors/custom-connectors)，这些连接器是现有 REST API 或 SOAP API 的包装器。 仅在单租户中，可以创建和使用[自定义内置连接器](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)。

下表列出了自定义连接器的值：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| 自定义连接器 | 每个 Azure 订阅 1,000 | 无限制 | 每个 Azure 订阅 1,000 ||
| 自定义连接器每分钟的请求数 | 每分钟每个连接 500 个请求 | 取决于你的实现 | 每分钟每个自定义连接器 2,000 个请求 ||
| 连接超时值 | 2 分钟 | 空闲连接： <br>4 分钟 <p><p>活动连接： <br>10 分钟 | 2 分钟 ||
||||||

有关详细信息，请查看以下文档：

* [自定义托管连接器概述](/connectors/custom-connectors)
* [启用内置连接器创作 - 使用 Azure 逻辑应用（标准版）扩展的 Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## <a name="managed-identity-limits"></a>托管标识限制

| 名称 | 限制 |
|------|-------|
| 每个逻辑应用的托管标识 | 系统分配的标识或 1 个用户分配的标识 |
| 每个区域的每个 Azure 订阅中具有托管标识的逻辑应用数量 | 1,000 |
|||

> [!NOTE] 
> 默认情况下，逻辑应用（标准版）资源自带系统分配的托管标识，该标识已自动启用以在运行时对连接进行身份验证。 该标识与你在创建连接时使用的身份验证凭据或连接字符串不同。 如果禁用该标识，则运行时连接无效。 若要查看此设置，请在逻辑应用菜单的“设置”下，选择“标识” 。

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>集成帐户限制

每个 Azure 订阅具有以下集成帐户限制：

* 每个 Azure 区域一个[免费层](../logic-apps/logic-apps-pricing.md#integration-accounts)集成帐户。 此级别仅适用于 Azure 中的公共区域（如“美国西部”或“东南亚”），但不适用于 [Azure 中国世纪互联](/azure/china/overview-operations)或 [Azure 政府](../azure-government/documentation-government-welcome.md)。

* 共 1,000 个集成帐户，包括[开发人员和高级 SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)中任何[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 内的集成帐户。

* 无论是[开发人员还是高级](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)层级，每个 ISE 都可以使用单个集成帐户，而无需额外付费，尽管包括的帐户类型因 ISE SKU 而异。 可以为 ISE 创建更多集成帐户，直到达到[额外费用](logic-apps-pricing.md#ise-pricing)的总限制。

  | ISE SKU | 集成帐户限制 |
  |---------|----------------------------|
  | **高级** | 共 20 个帐户，包括一个标准帐户，无需额外付费。 使用此 SKU，你只能拥有[标准](../logic-apps/logic-apps-pricing.md#integration-accounts)帐户。 不允许使用免费帐户或基本帐户。 |
  | **开发人员** | 共 20 个帐户，包括一个[免费](../logic-apps/logic-apps-pricing.md#integration-accounts)帐户（限制为 1）。 使用此 SKU，你可以有以下任意组合： <p>- 一个免费帐户和最多 19 个[标准](../logic-apps/logic-apps-pricing.md#integration-accounts)帐户。 <br>- 无免费帐户和最多 20 个标准帐户。 <p>不允许使用基本帐户或更多免费帐户。 <p><p>重要说明：[开发人员 SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 仅可用于试验、开发和测试，不可用于生产或性能测试。 |
  |||

要了解 ISE 的定价和计费原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#ise-pricing)。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>每个集成帐户的项目限制

下表列出了每个集成帐户层限制的项目数的值。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 若要了解集成帐户的定价和计费工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts)。

> [!NOTE]
> 免费层仅用于探索场景，不用于生产场景。 此层限制吞吐量和使用情况，并且不具有服务级别协议 (SLA)。

| 项目 | 免费 | 基本 | Standard |
|----------|------|-------|----------|
| EDI 贸易协议 | 10 | 1 | 1,000 |
| EDI 参与方 | 25 | 2 | 1,000 |
| 地图 | 25 | 500 | 1,000 |
| 架构 | 25 | 500 | 1,000 |
| 程序集 | 10 | 25 | 1,000 |
| 证书 | 25 | 2 | 1,000 |
| 批处理配置 | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>项目容量限制

| 项目 | 限制 | 说明 |
| -------- | ----- | ----- |
| Assembly | 8 MB | 若要上传大于 2 MB 的文件，请使用 [Azure 存储帐户和 blob 容器](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 |
| 映射（XSLT 文件） | 8 MB | 若要上传大于 2 MB 的文件，请使用 [Azure 逻辑应用 REST API - 映射](/rest/api/logic/maps/createorupdate)。 <p><p>**注意**：映射可以成功处理的数据或记录量取决于 Azure 逻辑应用中的消息大小和操作超时限制。 例如，如果使用 HTTP 操作，则根据 [HTTP 消息大小和超时限制](#http-limits)，在操作能够在 HTTP 超时限制内完成的情况下，映射最多可以处理达到 HTTP 消息大小限制的数据量。 |
| 架构 | 8 MB | 若要上传大于 2 MB 的文件，请使用 [Azure 存储帐户和 blob 容器](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>吞吐量限制

| 运行时终结点 | 免费 | 基本 | Standard | 说明 |
|------------------|------|-------|----------|-------|
| 每 5 分钟的读取调用数 | 3,000 | 30,000 | 60,000 | 此限制适用于从逻辑应用的运行历史记录获取原始输入和输出的调用。 你可根据需要在多个帐户之间分配工作负荷。 |
| 每 5 分钟调用的调用数 | 3,000 | 30,000 | 45,000 | 你可根据需要在多个帐户之间分配工作负荷。 |
| 每 5 分钟的跟踪调用数 | 3,000 | 30,000 | 45,000 | 你可根据需要在多个帐户之间分配工作负荷。 |
| 阻止并发调用 | ~1,000 | ~1,000 | ~1,000 | 对于所有 SKU 均相同。 你可减少并发请求数，或根据需要减少持续时间。 |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B 协议（AS2、X12、EDIFACT）消息大小

下表列出了适用于 B2B 协议的消息大小限制：

| 名称 | 多租户 | 单租户 | 集成服务环境 | 注释 |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 25 MB | 不可用 | v2 - 200 MB <br>v1 - 25 MB | 适用于解码和编码 |
| X12 | 50 MB | 不可用 | 50 MB | 适用于解码和编码 |
| EDIFACT | 50 MB | 不可用 | 50 MB | 适用于解码和编码 |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>防火墙配置：IP 地址和服务标记

如果你的环境具有严格的网络要求或具有仅限流量流经特定 IP 地址的防火墙，则你的环境或防火墙需要允许访问逻辑应用资源所在的 Azure 区域中的 Azure 逻辑应用服务或运行时使用的[入站](#inbound)和[出站](#outbound) IP 地址。 若要设置此访问权限，可以创建 [Azure 防火墙规则](../firewall/rule-processing.md)。 同一区域中的所有逻辑应用都使用相同的 IP 地址范围。

> [!NOTE]
> 如果使用的是 [Power Automate](/power-automate/getting-started)，则某些操作（例如 HTTP 和 HTTP + OpenAPI）将直接通过 Azure 逻辑应用服务传输并来自此处列出的 IP 地址。 有关 Power Automate 使用的 IP 地址的详细信息，请参阅 [Power Automate 的限制和配置](/flow/limits-and-config#ip-address-configuration)。

例如，假设你的逻辑应用已部署到美国西部区域。 若要支持逻辑应用通过内置触发器和操作（如 [HTTP 触发器或操作](../connectors/connectors-native-http.md)）发送或接收的调用，你的防火墙需要允许访问美国西部区域中存在的所有 Azure 逻辑应用服务入站 IP 地址和出站 IP 地址 。

如果工作流使用[托管连接器](../connectors/managed.md)（例如 Office 365 Outlook 连接器或 SQL 连接器），或使用[自定义连接器](/connectors/custom-connectors/)，则防火墙还需要允许从逻辑应用所在 Azure 区域中的所有[托管连接器出站 IP 地址](/connectors/common/outbound-ip-addresses)进行访问。 如果工作流使用通过 [Azure 中的本地数据网关资源](logic-apps-gateway-connection.md)来访问本地资源的自定义连接器，则需要设置网关安装，以便允许从相应的[托管连接器出站 IP 地址](/connectors/common/outbound-ip-addresses)进行访问。 若要详细了解如何设置网关的通信设置，请参阅以下主题：

* [调整本地数据网关的通信设置](/data-integration/gateway/service-gateway-communication)
* [为本地数据网关配置代理设置](/data-integration/gateway/service-gateway-proxy)

> [!IMPORTANT]
> 如果使用的是[世纪互联运营的 Microsoft Azure](/azure/china/)，则托管连接器和自定义连接器没有预留的或固定的 IP 地址。 因此，无法为在此云中使用这些连接器的逻辑应用设置防火墙规则。 对于 Azure 逻辑应用服务 IP，请参阅[世纪互联运营的 Azure 的文档版本](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration)。

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>防火墙 IP 配置注意事项

在设置具有 IP 地址的防火墙之前，请查看以下注意事项：

* 如果逻辑应用工作流在单租户 Azure 逻辑应用中运行，则需要查找连接的完全限定的域名 (FQDN)。 有关详细信息，请查看以下主题中的相应部分：

  * [单租户逻辑应用的防火墙权限 - Azure 门户](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [单租户逻辑应用的防火墙权限 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* 若要在[集成服务环境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) 中运行逻辑应用工作流，请确保[这些端口也是打开的](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)。

* 为帮助你简化要创建的任何安全规则，可选择性地使用[服务标记](../virtual-network/service-tags-overview.md)，而不是为每个区域指定 IP 地址前缀。 这些标记适用于可使用逻辑应用服务的区域：

  * **LogicAppsManagement**：表示逻辑应用服务的入站 IP 地址前缀。

  * **LogicApps**：表示逻辑应用服务的出站 IP 地址前缀。

  * AzureConnectors：表示托管连接器的 IP 地址前缀，这些托管连接器对逻辑应用服务进行入站 Webhook 回调，并对其各自的服务（例如 Azure 存储或 Azure 事件中心）进行出站调用。

* 如果你的逻辑应用在访问使用[防火墙和防火墙规则](../storage/common/storage-network-security.md)的 Azure 存储帐户时遇到问题，可采用[多种其他方式来实现访问](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)。

  例如，逻辑应用不能直接访问使用防火墙规则的存储帐户，因此存在于同一区域中。 但是，如果允许[区域中托管连接器的出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)，则逻辑应用可以访问其他区域中的存储帐户，除非你使用 Azure 表存储或 Azure 队列存储连接器。 若要访问表存储或队列存储，则可改用 HTTP 触发器和操作。 有关其他选项，请参阅[访问防火墙后的存储帐户](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)。

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>入站 IP 地址

该部分仅列出了 Azure 逻辑应用服务的入站 IP 地址。 如果使用的是 Azure 政府，请参阅 [Azure 政府 - 入站 IP 地址](#azure-government-inbound)。

> [!TIP]
> 为帮助你更简单地创建安全规则，可选择性地使用[服务标记](../virtual-network/service-tags-overview.md) LogicAppsManagement，而不是为每个区域指定入站逻辑应用 IP 地址前缀。
>
> 某些托管连接器会对 Azure 逻辑应用服务进行入站 Webhook 回调。 对于这些托管连接器，你可以选择使用 AzureConnectors 服务标记，不必为每个区域指定入站托管连接器 IP 地址前缀。 
> 这些标记适用于可使用逻辑应用服务的区域。
>
> 以下连接器对逻辑应用服务进行入站 Webhook 回调：
>
> Adobe Creative Cloud、Adobe Sign、Adobe Sign 演示版、Adobe Sign 预览版、Adobe Sign 阶段版、Azure Sentinel、Business Central、Calendly、Common Data Service、DocuSign、DocuSign 演示版、Dynamics 365 for Fin & Ops、LiveChat、Office 365 Outlook、Outlook.com、Parserr、SAP*、Shifts for Microsoft Teams、Teamwork Projects、Typeform
>
> SAP\*：返回调用方取决于部署环境是多租户 Azure 还是 ISE。 在多租户环境中，本地数据网关回调到逻辑应用服务。 在 ISE 中，SAP 连接器回调到逻辑应用服务。

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant--single-tenant---inbound-ip-addresses"></a>多租户和单租户 - 入站 IP 地址

| 区域 | IP |
|--------|----|
| 澳大利亚东部 | 13.75.153.66、104.210.89.222、104.210.89.244、52.187.231.161 |
| 澳大利亚东南部 | 13.73.115.153、40.115.78.70、40.115.78.237、52.189.216.28 |
| 巴西南部 | 191.235.86.199、191.235.95.229、191.235.94.220、191.234.166.198 |
| 巴西东南部 | 20.40.32.59、20.40.32.162、20.40.32.80、20.40.32.49 |
| 加拿大中部 | 13.88.249.209、52.233.30.218、52.233.29.79、40.85.241.105 |
| 加拿大东部 | 52.232.129.143、52.229.125.57、52.232.133.109、40.86.202.42 |
| 印度中部 | 52.172.157.194、52.172.184.192、52.172.191.194、104.211.73.195 |
| 美国中部 | 13.67.236.76、40.77.111.254、40.77.31.87、104.43.243.39 |
| 东亚 | 168.63.200.173、13.75.89.159、23.97.68.172、40.83.98.194 |
| 美国东部 | 137.135.106.54、40.117.99.79、40.117.100.228、137.116.126.165 |
| 美国东部 2 | 40.84.25.234、40.79.44.7、40.84.59.136、40.70.27.253 |
| 法国中部 | 52.143.162.83、20.188.33.169、52.143.156.55、52.143.158.203 |
| 法国南部 | 52.136.131.145、52.136.129.121、52.136.130.89、52.136.131.4 |
| 德国北部 | 51.116.211.29、51.116.208.132、51.116.208.37、51.116.208.64 |
| 德国中西部 | 51.116.168.222、51.116.171.209、51.116.233.40、51.116.175.0 |
| 日本东部 | 13.71.146.140、13.78.84.187、13.78.62.130、13.78.43.164 |
| 日本西部 | 40.74.140.173、40.74.81.13、40.74.85.215、40.74.68.85 |
| 韩国中部 | 52.231.14.182、52.231.103.142、52.231.39.29、52.231.14.42 |
| 韩国南部 | 52.231.166.168、52.231.163.55、52.231.163.150、52.231.192.64 |
| 美国中北部 | 168.62.249.81、157.56.12.202、65.52.211.164、65.52.9.64 |
| 北欧 | 13.79.173.49、52.169.218.253、52.169.220.174、40.112.90.39 |
| 挪威东部 | 51.120.88.93、51.13.66.86、51.120.89.182、51.120.88.77 |
| 南非北部 | 102.133.228.4、102.133.224.125、102.133.226.199、102.133.228.9 |
| 南非西部 | 102.133.72.190、102.133.72.145、102.133.72.184、102.133.72.173 |
| 美国中南部 | 13.65.98.39、13.84.41.46、13.84.43.45、40.84.138.132 |
| 印度南部 | 52.172.9.47、52.172.49.43、52.172.51.140、104.211.225.152 |
| 东南亚 | 52.163.93.214、52.187.65.81、52.187.65.155、104.215.181.6 |
| 瑞士北部 | 51.103.128.52、51.103.132.236、51.103.134.138、51.103.136.209 |
| 瑞士西部 | 51.107.225.180、51.107.225.167、51.107.225.163、51.107.239.66 |
| 阿联酋中部 | 20.45.75.193、20.45.64.29、20.45.64.87、20.45.71.213 |
| 阿拉伯联合酋长国北部 | 20.46.42.220、40.123.224.227、40.123.224.143、20.46.46.173 |
| 英国南部 | 51.140.79.109、51.140.78.71、51.140.84.39、51.140.155.81 |
| 英国西部 | 51.141.48.98、51.141.51.145、51.141.53.164、51.141.119.150 |
| 美国中西部 | 52.161.26.172、52.161.8.128、52.161.19.82、13.78.137.247 |
| 西欧 | 13.95.155.53、52.174.54.218、52.174.49.6 |
| 印度西部 | 104.211.164.112、104.211.165.81、104.211.164.25、104.211.157.237 |
| 美国西部 | 52.160.90.237、138.91.188.137、13.91.252.184、157.56.160.212 |
| 美国西部 2 | 13.66.224.169、52.183.30.10、52.183.39.67、13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure 政府 - 入站 IP 地址

| Azure 政府区域 | IP |
|-------------------------|----|
| US Gov 亚利桑那州 | 52.244.67.164、52.244.67.64、52.244.66.82 |
| US Gov 德克萨斯州 | 52.238.119.104、52.238.112.96、52.238.119.145 |
| US Gov 弗吉尼亚州 | 52.227.159.157、52.227.152.90、23.97.4.36 |
| US DoD 中部 | 52.182.49.204、52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>出站 IP 地址

此部分列出了 Azure 逻辑应用服务的出站 IP 地址。 如果使用的是 Azure 政府，请参阅 [Azure 政府 - 出站 IP 地址](#azure-government-outbound)。

> [!TIP]
> 为帮助你更简单地创建安全规则，可选择性地使用[服务标记](../virtual-network/service-tags-overview.md) LogicApps，而不是为每个区域指定出站逻辑应用 IP 地址前缀。 此外，还可以使用托管连接器的 AzureConnectors 服务标记，这些托管连接器对其各自的服务（例如 Azure 存储或 Azure 事件中心）进行出站调用，而不是为每个区域指定出站托管连接器 IP 地址前缀。 这些标记适用于可使用逻辑应用服务的区域。

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant--single-tenant---outbound-ip-addresses"></a>多租户和单租户 - 出站 IP 地址

| 区域 | 逻辑应用 IP |
|--------|---------------|
| 澳大利亚东部 | 13.75.149.4、104.210.91.55、104.210.90.241、52.187.227.245、52.187.226.96、52.187.231.184、52.187.229.130、52.187.226.139 |
| 澳大利亚东南部 | 13.73.114.207、13.77.3.139、13.70.159.205、52.189.222.77、13.77.56.167、13.77.58.136、52.189.214.42、52.189.220.75 |
| 巴西南部 | 191.235.82.221、191.235.91.7、191.234.182.26、191.237.255.116、191.234.161.168、191.234.162.178、191.234.161.28、191.234.162.131 |
| 巴西东南部 | 20.40.32.81、20.40.32.19、20.40.32.85、20.40.32.60、20.40.32.116、20.40.32.87、20.40.32.61、20.40.32.113 |
| 加拿大中部 | 52.233.29.92、52.228.39.244、40.85.250.135、40.85.250.212、13.71.186.1、40.85.252.47、13.71.184.150 |
| 加拿大东部 | 52.232.128.155、52.229.120.45、52.229.126.25、40.86.203.228、40.86.228.93、40.86.216.241、40.86.226.149、40.86.217.241 |
| 印度中部 | 52.172.154.168、52.172.186.159、52.172.185.79、104.211.101.108、104.211.102.62、104.211.90.169、104.211.90.162、104.211.74.145 |
| 美国中部 | 13.67.236.125、104.208.25.27、40.122.170.198、40.113.218.230、23.100.86.139、23.100.87.24、23.100.87.56、23.100.82.16 |
| 东亚 | 13.75.94.173、40.83.127.19、52.175.33.254、40.83.73.39、65.52.175.34、40.83.77.208、40.83.100.69、40.83.75.165 |
| 美国东部 | 13.92.98.111、40.121.91.41、40.114.82.191、23.101.139.153、23.100.29.190、23.101.136.201、104.45.153.81、23.101.132.208 |
| 美国东部 2 | 40.84.30.147、104.208.155.200、104.208.158.174、104.208.140.40、40.70.131.151、40.70.29.214、40.70.26.154、40.70.27.236 |
| 法国中部 | 52.143.164.80、52.143.164.15、40.89.186.30、20.188.39.105、40.89.191.161、40.89.188.169、40.89.186.28、40.89.190.104 |
| 法国南部 | 52.136.132.40、52.136.129.89、52.136.131.155、52.136.133.62、52.136.139.225、52.136.130.144、52.136.140.226、52.136.129.51 |
| 德国北部 | 51.116.211.168、51.116.208.165、51.116.208.175、51.116.208.192、51.116.208.200、51.116.208.222、51.116.208.217、51.116.208.51 |
| 德国中西部 | 51.116.233.35、51.116.171.49、51.116.233.33、51.116.233.22、51.116.168.104、51.116.175.17、51.116.233.87、51.116.175.51 |
| 日本东部 | 13.71.158.3、13.73.4.207、13.71.158.120、13.78.18.168、13.78.35.229、13.78.42.223、13.78.21.155、13.78.20.232 |
| 日本西部 | 40.74.140.4、104.214.137.243、138.91.26.45、40.74.64.207、40.74.76.213、40.74.77.205、40.74.74.21、40.74.68.85 |
| 韩国中部 | 52.231.14.11、52.231.14.219、52.231.15.6、52.231.10.111、52.231.14.223、52.231.77.107、52.231.8.175、52.231.9.39 |
| 韩国南部 | 52.231.204.74、52.231.188.115、52.231.189.221、52.231.203.118、52.231.166.28、52.231.153.89、52.231.155.206、52.231.164.23 |
| 美国中北部 | 168.62.248.37、157.55.210.61、157.55.212.238、52.162.208.216、52.162.213.231、65.52.10.183、65.52.9.96、65.52.8.225 |
| 北欧 | 40.113.12.95、52.178.165.215、52.178.166.21、40.112.92.104、40.112.95.216、40.113.4.18、40.113.3.202、40.113.1.181 |
| 挪威东部 | 51.120.88.52、51.120.88.51、51.13.65.206、51.13.66.248、51.13.65.90、51.13.65.63、51.13.68.140、51.120.91.248 |
| 南非北部 | 102.133.231.188、102.133.231.117、102.133.230.4、102.133.227.103、102.133.228.6、102.133.230.82、102.133.231.9、102.133.231.51 |
| 南非西部 | 102.133.72.98、102.133.72.113、102.133.75.169、102.133.72.179、102.133.72.37、102.133.72.183、102.133.72.132、102.133.75.191 |
| 美国中南部 | 104.210.144.48、13.65.82.17、13.66.52.232、23.100.124.84、70.37.54.122、70.37.50.6、23.100.127.172、23.101.183.225 |
| 印度南部 | 52.172.50.24、52.172.55.231、52.172.52.0、104.211.229.115、104.211.230.129、104.211.230.126、104.211.231.39、104.211.227.229 |
| 东南亚 | 13.76.133.155、52.163.228.93、52.163.230.166、13.76.4.194、13.67.110.109、13.67.91.135、13.76.5.96、13.67.107.128 |
| 瑞士北部 | 51.103.137.79、51.103.135.51、51.103.139.122、51.103.134.69、51.103.138.96、51.103.138.28、51.103.136.37、51.103.136.210 |
| 瑞士西部 | 51.107.239.66、51.107.231.86、51.107.239.112、51.107.239.123、51.107.225.190、51.107.225.179、51.107.225.186、51.107.225.151、51.107.239.83 |
| 阿联酋中部 | 20.45.75.200、20.45.72.72、20.45.75.236、20.45.79.239、20.45.67.170、20.45.72.54、20.45.67.134、20.45.67.135 |
| 阿拉伯联合酋长国北部 | 40.123.230.45、40.123.231.179、40.123.231.186、40.119.166.152、40.123.228.182、40.123.217.165、40.123.216.73、40.123.212.104 |
| 英国南部 | 51.140.74.14、51.140.73.85、51.140.78.44、51.140.137.190、51.140.153.135、51.140.28.225、51.140.142.28、51.140.158.24 |
| 英国西部 | 51.141.54.185、51.141.45.238、51.141.47.136、51.141.114.77、51.141.112.112、51.141.113.36、51.141.118.119、51.141.119.63 |
| 美国中西部 | 52.161.27.190、52.161.18.218、52.161.9.108、13.78.151.161、13.78.137.179、13.78.148.140、13.78.129.20、13.78.141.75 |
| 西欧 | 40.68.222.65、40.68.209.23、13.95.147.65、23.97.218.130、51.144.182.201、23.97.211.179、104.45.9.52、23.97.210.126、13.69.71.160、13.69.71.161、13.69.71.162、13.69.71.163、13.69.71.164、13.69.71.165、13.69.71.166、13.69.71.167 |
| 印度西部 | 104.211.164.80、104.211.162.205、104.211.164.136、104.211.158.127、104.211.156.153、104.211.158.123、104.211.154.59、104.211.154.7 |
| 美国西部 | 52.160.92.112、40.118.244.241、40.118.241.243、157.56.162.53、157.56.167.147、104.42.49.145、40.83.164.80、104.42.38.32、13.86.223.0、13.86.223.1、13.86.223.2、13.86.223.3、13.86.223.4、13.86.223.5 |
| 美国西部 2 | 13.66.210.167、52.183.30.169、52.183.29.132、13.66.210.167、13.66.201.169、13.77.149.159、52.175.198.132、13.66.246.219 |
|||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure 政府 - 出站 IP 地址

| 区域 | 逻辑应用 IP |
|--------|---------------|
| US DoD 中部 | 52.182.48.215、52.182.92.143 |
| US Gov 亚利桑那州 | 52.244.67.143、52.244.65.66、52.244.65.190 |
| US Gov 德克萨斯州 | 52.238.114.217、52.238.115.245、52.238.117.119 |
| US Gov 弗吉尼亚州 | 13.72.54.205、52.227.138.30、52.227.152.44 |
|||

## <a name="next-steps"></a>后续步骤

* 了解如何[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* 了解[常见示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
