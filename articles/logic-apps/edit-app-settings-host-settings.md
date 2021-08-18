---
title: 在单租户 Azure 逻辑应用中编辑运行时和环境设置
description: 在单租户 Azure 逻辑应用中更改逻辑应用的运行时和环境设置。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: cf1361a531511daf7f249f4e7c7d2acf9207c457
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743247"
---
# <a name="edit-host-and-app-settings-for-logic-apps-in-single-tenant-azure-logic-apps"></a>在单租户 Azure 逻辑应用中编辑逻辑应用的主机和应用设置

在单租户 Azure 逻辑应用中，逻辑应用的应用设置指定会影响该逻辑应用中所有工作流的全局配置选项  。 但是，这些设置仅在这些工作流在本地开发环境中运行时适用 。 在本地运行时，工作流可以将这些应用设置作为本地环境变量来访问，本地开发工具将这些变量用于可能经常在环境之间更改的值。 例如，这些值可以包含连接字符串。 部署到 Azure 时，应用设置将被忽略并且不包含在部署中。

逻辑应用还具有主机设置，这些设置指定用于该逻辑应用中所有工作流的运行时配置设置和值，例如吞吐量、容量、数据大小等的默认值，无论它们是在本地运行还是在 Azure 中运行  。

<a name="app-settings-parameters-deployment"></a>

## <a name="app-settings-parameters-and-deployment"></a>应用设置、参数和部署

在多租户 Azure 逻辑应用中，资源部署依赖于 Azure 资源管理器模板（ARM 模板），部署中将组合这些模板以预配逻辑应用和基础结构的资源。 当需要维护各种不同的开发、测试和生产环境中的逻辑应用的环境变量时，这种设计会带来困难。 ARM 模板中的所有内容都是在部署时定义的。 如果只需更改单个变量，必须重新部署所有内容。

在单租户 Azure 逻辑应用中，部署会容易一些，因为可以将应用与基础结构的资源预配相分离。 可以使用参数来抽象化可能在环境之间发生更改的值。 通过定义工作流中使用的参数，可以先专注于工作流的设计，然后稍后插入特定于环境的变量。 可以使用应用设置和参数在运行时调用和引用环境变量。 这样，就不必经常重新部署。

应用设置与 Azure Key Vault 集成。 可以[直接引用安全字符串](../app-service/app-service-key-vault-references.md)，例如连接字符串和密钥。 与 Azure 资源管理器模板（ARM 模板）类似，可以在部署时定义环境变量，可以在[逻辑应用工作流定义](/azure/templates/microsoft.logic/workflows)中定义应用设置。 然后，可以捕获动态生成的基础结构值，例如连接终结点、存储字符串等。 但是，应用设置具有大小限制，无法从 Azure 逻辑应用中的某些区域引用。

有关设置逻辑应用以进行部署的详细信息，请参阅以下文档：

- [为在不同单租户 Azure 逻辑应用环境的工作流中会有所不同的值创建参数](parameterize-workflow-app.md)
- [基于单租户的逻辑应用的 DevOps 部署概述](devops-deployment-single-tenant-azure-logic-apps.md)
- [为基于单租户逻辑应用设置 DevOps 部署](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="visual-studio-code-project-structure"></a>Visual Studio Code 项目结构

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="reference-local-settings-json"></a>

## <a name="reference-for-app-settings---localsettingsjson"></a>应用设置参考 - local.settings.json

在 Visual Studio Code 中逻辑应用项目的根级别，local.settings.json 文件包含全局配置选项，在本地开发环境中运行时这些选项会影响该逻辑应用中的所有工作流。 当工作流在本地运行时，系统将这些设置作为本地环境变量进行访问，并且它们的值通常在运行工作流的各种环境中会有所不同。 若要查看和管理这些设置，请查看[管理应用设置 - local.settings.json](#manage-app-settings)。

Azure 逻辑应用中的应用设置的作用方式与 Azure Functions 或 Azure Web 应用中的应用设置的作用方式类似。 如果你以前使用过这些其他服务，可能已熟悉应用设置。 有关详细信息，请查看 [Azure Functions 的应用设置参考](../azure-functions/functions-app-settings.md)和[使用 Azure Functions Core Tools - 本地设置文件](../azure-functions/functions-develop-local.md#local-settings-file)。

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `AzureWebJobsStorage` | 无 | 设置 Azure 存储帐户的连接字符串。 |
| `Workflows.<workflowName>.FlowState` | 无 | 设置 <workflowName> 的状态。 |
| `Workflows.<workflowName>.RuntimeConfiguration.RetentionInDays` | 无 | 设置 <workflowName> 的操作选项。 |
| `Workflows.Connection.AuthenticationAudience` | 无 | 设置要对 Azure 托管连接进行身份验证的访问群图。 |
| `Workflows.WebhookRedirectHostUri` | 无 | 设置用于 Webhook 回调 URL 的主机名。 |
| `WEBSITE_LOAD_ROOT_CERTIFICATES` | 无 | 设置受信任的根证书的指纹。 |
||||

<a name="manage-app-settings"></a>

## <a name="manage-app-settings---localsettingsjson"></a>管理应用设置 - local.settings.json

若要添加、更新或删除应用设置，请选择并查看 Visual Studio Code、Azure 门户、Azure CLI 或 ARM (Bicep) 模板的以下部分。 有关特定于逻辑应用的应用设置，请查看[可用应用设置的参考指南 - local.settings.json](#reference-local-settings-json)。

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

若要在 Visual Studio Code 中查看逻辑应用的应用设置，请执行以下步骤：

1. 在逻辑应用项目的根项目级别，找到并打开 local.settings.json 文件。

1. 在 `Values` 对象中，查看逻辑应用的应用设置。

   有关这些设置的详细信息，请查看[可用应用设置的参考指南 - local.settings.json](#reference-local-settings-json)。

若要添加应用设置，请执行以下步骤：

1. 在 local.settings.json 文件中，找到 `Values` 对象。

1. 在 `Values` 对象中，添加在 Visual Studio Code 中本地运行时要应用的应用设置。 某些设置允许为特定工作流添加设置，例如：

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "Workflows.WorkflowName1.FlowState" : "Disabled",
         <...>
     }
   }
   ```

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

若要在 Azure 门户中查看基于单租户的逻辑应用的应用设置，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)搜索框中，找到并打开你的逻辑应用。

1. 在逻辑应用菜单上的“设置”下，选择“配置” 。

1. 在“配置”页上的“应用程序设置”选项卡上，查看逻辑应用的应用设置 。

   有关这些设置的详细信息，请查看[可用应用设置的参考指南 - local.settings.json](#reference-local-settings-json)。

1. 若要查看所有值，请选择“显示值”。 或者，若要查看单个值，请选择该值。

若要添加设置，请执行以下步骤：

1. 在“应用程序设置”选项卡上的“应用程序设置”下，选择“新建应用程序设置”  。

1. 对于“名称”，输入新设置的键或名称。

1. 对于“值”，输入新设置的值。

1. 准备好创建新的键值对时，请选择“确定”。

:::image type="content" source="./media/edit-app-settings-host-settings/portal-app-settings-values.png" alt-text="屏幕截图显示 Azure 门户和配置窗格，其中包含基于单租户的逻辑应用的应用设置和值。" lightbox="./media/edit-app-settings-host-settings/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 查看当前应用设置，请运行 `az logicapp config appsettings list` 命令。 请确保命令包含 `--name -n` 和 `--resource-group -g` 参数，例如：

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

有关这些设置的详细信息，请查看[可用应用设置的参考指南 - local.settings.json](#reference-local-settings-json)。

若要使用 Azure CLI 添加或更新应用设置，请运行命令 `az logicapp config appsettings set`。 请确保命令包含 `--name n` 和 `--resource-group -g` 参数。 例如，以下命令可用于创建一个设置，该设置具有名为 `CUSTOM_LOGIC_APP_SETTING` 的键，其值为 `12345`：

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

---

<a name="reference-host-json"></a>

## <a name="reference-for-host-settings---hostjson"></a>主机设置参考 - host.json

在 Visual Studio Code 中逻辑应用项目的根级别，host.json 元数据文件包含运行时设置和默认值，这些设置将用于逻辑应用资源中的所有工作流，无论是在本地运行还是 Azure 中运行。 若要查看和管理这些设置，请查看[管理主机设置 - host.json](#manage-host-settings)。 若要了解相关限制的信息，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#definition-limits)文档。

<a name="job-orchestration"></a>

### <a name="job-orchestration-throughput"></a>作业业务流程吞吐量

这些设置会影响单租户 Azure 逻辑应用运行工作流操作时的吞吐量和容量。

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Jobs.BackgroundJobs.DispatchingWorkersPulseInterval` | `00:00:01` <br>（1 秒） | 设置作业调度程序在上一次轮询未返回任何作业时对作业队列的轮询时间间隔。 作业调度程序会在上一个轮询返回作业时立即轮询队列。 |
| `Jobs.BackgroundJobs.NumWorkersPerProcessorCount` | `192` 调度程序辅助角色实例 | 设置每个处理器核心要具有的调度程序辅助角色实例数或作业调度程序数 。 此值影响每个核心的工作流运行数。 |
| `Jobs.BackgroundJobs.NumPartitionsInJobTriggersQueue` | `1` 个作业队列 | 设置作业调度程序针对要处理的作业所监视的作业队列数。 此值还会影响作业队列所在的存储分区数量。 |
| `Jobs.BackgroundJobs.NumPartitionsInJobDefinitionsTable` | `4` 个作业分区 | 设置作业定义表中的作业分区数。 此值控制分区存储限制会影响多少执行吞吐量。 |
||||

<a name="run-duration-history"></a>

### <a name="run-duration-and-history"></a>运行持续时间和历史记录

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.FlowRetentionThreshold` | `90.00:00:00` <br>（90 天） | 设置运行开始后保留工作流运行历史记录的时间长度。 |
| `Runtime.Backend.FlowRunTimeout` | `90.00:00:00` <br>（90 天） | 设置在强制执行超时之前工作流可以继续运行的时间长度。 <p><p>**重要说明**：请确保此值小于或等于 `Runtime.FlowRetentionThreshold` 值。 否则，运行历史记录可能会在关联的作业完成之前被删除。 |
||||

<a name="inputs-outputs"></a>

### <a name="inputs-and-outputs"></a>输入和输出

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.FlowRunActionJob.MaximumActionResultSize` | `209715200` 字节 | 设置操作中的组合输入和输出可包含的最大字节数。 |
| `Runtime.ContentLink.MaximumContentSizeInBytes` | `104857600` 个字符 | 设置触发器或操作中的输入或输出可包含的最大字符数。 |
||||

<a name="pagination"></a>

### <a name="pagination"></a>分页

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPageCount` | `1000` 页 | 如果对某个操作支持并启用了分页，则设置在运行时要返回或处理的最大页数。 |
||||

<a name="chunking"></a>

### <a name="chunking"></a>分块

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumContentLengthInBytesForPartialContent` | `1073741824` 字节 | 如果对某个操作支持并启用了分块，则设置下载内容或上传内容的最大字节数。 |
| `Runtime.FlowRunRetryableActionJobCallback.MaxChunkSizeInBytes` | `52428800` 字节 | 如果对某个操作支持并启用了分块，则设置每个内容区块的最大字节数。 |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumRequestCountForPartialContent` | `1000` 个请求 | 如果对某个操作支持并启用了分块，则设置每个操作执行可针对下载内容发出的最大请求数。 |
||||

<a name="trigger-concurrency"></a>

### <a name="trigger-concurrency"></a>触发器并发

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Trigger.MaximumRunConcurrency` | `100` 次运行 | 设置触发器可启动的最大并发运行数。 此值显示在触发器的并发定义中。 |
| `Runtime.Trigger.MaximumWaitingRuns` | `200` 次运行 | 设置并发运行达到最大值后可等待的最大运行数。 此值显示在触发器的并发定义中。 |
||||

<a name="for-each-loop"></a>

### <a name="for-each-loops"></a>For each 循环

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.FlowDefaultForeachItemsLimit` | `100000` <br>（10 万个数组项） | 对于有状态工作流，设置 `For each` 循环中要处理的最大数组项数。 |
| `Runtime.Backend.Stateless.FlowDefaultForeachItemsLimit` | `100` 项 | 对于无状态工作流，设置 `For each` 循环中要处理的最大数组项数。 |
| `Runtime.Backend.ForeachDefaultDegreeOfParallelism` | `20` 次迭代 | 设置 `For each` 循环中的默认并发迭代次数或默认并行度。 若要按顺序运行，将值设置为 `1`。 |
| `Runtime.Backend.FlowDefaultSplitOnItemsLimit` | `100000` <br>（10 万个数组项） | 根据 `SplitOn` 设置，设置要分解或拆分为多个工作流实例的最大数组项数。 |
||||

<a name="until-loop"></a>

### <a name="until-loops"></a>Until 循环

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.MaximumUntilLimitCount` | `5000` 次迭代 | 对于有状态工作流，设置 `Until` 操作中 `Count` 属性的最大可能数。 |
| `Runtime.Backend.Stateless.MaximumUntilLimitCount` | `100` 次迭代 | 对于无状态工作流，设置 `Until` 操作中 `Count` 属性的最大可能数。 |
| `Runtime.Backend.Stateless.FlowRunTimeout` | `00:05:00` <br>（5 分钟） | 设置无状态工作流中 `Until` 循环的最长等待时间。 |
||||

<a name="variables"></a>

### <a name="variables"></a>变量

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.DefaultAppendArrayItemsLimit` | `100000` <br>（10 万个数组项） | 设置数组类型的变量中的最大项数。 |
| `Runtime.Backend.VariableOperation.MaximumVariableSize` | 有状态工作流：`104857600` 个字符 <p><p>无状态工作流：`1024` 个字符 | 设置变量可存储的内容的最大字符数。 |
||||

<a name="http-webhook"></a>

### <a name="http-operations"></a>HTTP 操作

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.HttpOperation.RequestTimeout` | `00:03:45` <br>（3 分 45 秒） | 设置 HTTP 触发器和操作的请求超时值。 |
| `Runtime.Backend.HttpOperation.MaxContentSize` | `104857600` 字节 | 设置 HTTP 触发器和操作的最大请求大小（以字节为单位）。 |
| `Runtime.Backend.HttpOperation.DefaultRetryCount` | `4` 次重试 | 设置 HTTP 触发器和操作的默认重试次数。 |
| `Runtime.Backend.HttpOperation.DefaultRetryInterval` | `00:00:07` <br>（7 秒） | 设置 HTTP 触发器和操作的默认重试间隔。 |
| `Runtime.Backend.HttpOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>（1 小时） | 设置 HTTP 触发器和操作的最大重试间隔。 |
| `Runtime.Backend.HttpOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>（5 秒） | 设置 HTTP 触发器和操作的最小重试间隔。 |
||||

<a name="http-webhook"></a>

### <a name="http-webhook-operations"></a>HTTP Webhook 操作

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.HttpWebhookOperation.RequestTimeout` | `00:02:00` <br>（2 分钟） | 设置 HTTP Webhook 触发器和操作的请求超时值。 |
| `Runtime.Backend.HttpWebhookOperation.MaxContentSize` | `104857600` 字节 | 设置 HTTP Webhook 触发器和操作的最大请求大小（以字节为单位）。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryCount` | `4` 次重试 | 设置 HTTP Webhook 触发器和操作的默认重试次数。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryInterval` | `00:00:07` <br>（7 秒） | 设置 HTTP Webhook 触发器和操作的默认重试间隔。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>（1 小时） | 设置 HTTP Webhook 触发器和操作的最大重试间隔。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>（5 秒） | 设置 HTTP Webhook 触发器和操作的最小重试间隔。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>（1 小时） | 设置 HTTP Webhook 触发器和操作作业的默认唤醒间隔。 |
||||

<a name="built-in-azure-functions"></a>

### <a name="built-in-azure-functions-operations"></a>内置 Azure Functions 操作

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.FunctionOperation.RequestTimeout` | `00:03:45` <br>（3 分 45 秒） | 设置 Azure Functions 操作的请求超时值。 |
| `Runtime.Backend.FunctionOperation.MaxContentSize` | `104857600` 字节 | 设置 Azure Functions 操作的最大请求大小（以字节为单位）。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryCount` | `4` 次重试 | 设置 Azure Functions 操作的默认重试次数。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryInterval` | `00:00:07` <br>（7 秒） | 设置 Azure Functions 操作的默认重试间隔。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>（1 小时） | 设置 Azure Functions 操作的最大重试间隔。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>（5 秒） | 设置 Azure Functions 操作的最小重试间隔。 |
||||

<a name="built-in-sql"></a>

### <a name="built-in-sql-operations"></a>内置 SQL 操作

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.ServiceProviders.Sql.QueryExecutionTimeout` | `00:00:30` <br>（30 秒） | 设置 SQL 服务提供程序操作的请求超时值。 |
||||

<a name="built-in-service-bus"></a>

### <a name="built-in-azure-service-bus-operations"></a>内置 Azure 服务总线操作

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.ServiceProviders.ServiceBus.MessageSenderPoolSizePerProcessorCount` | `64` 个消息发送程序 | 设置要在消息发送程序池中使用的每个处理器核心的 Azure 服务总线消息发送程序数。 |
||||

<a name="managed-api-connector"></a>

### <a name="managed-api-connector-operations"></a>托管 API 连接器操作

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.Backend.ApiConnectionOperation.RequestTimeout` | `00:02:00` <br>（2 分钟） | 设置托管 API 连接器触发器和操作的请求超时值。 |
| `Runtime.Backend.ApiConnectionOperation.MaxContentSize` | `104857600` 字节 | 设置托管 API 连接器触发器和操作的最大请求大小（以字节为单位）。 |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryCount` | `4` 次重试 | 设置托管 API 连接器触发器和操作的默认重试次数。 |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryInterval` | `00:00:07` <br>（7 秒） | 设置托管 API 连接器触发器和操作的默认重试间隔。 |
| `Runtime.Backend.ApiWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>（1 天） | 设置托管 API 连接器 Webhook 触发器和操作的最大重试间隔时间。 |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>（5 秒） | 设置托管 API 连接器触发器和操作的最小重试间隔。 |
| `Runtime.Backend.ApiWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>（1 天） | 设置托管 API 连接器 Webhook 触发器和操作作业的默认唤醒间隔时间。 |
||||

<a name="blob-storage"></a>

### <a name="blob-storage"></a>Blob 存储

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.ContentStorage.RequestOptionsServerTimeout` | `00:00:30` <br>（30 秒） | 设置来自 Azure 逻辑应用运行时的 Blob 请求的超时值。 |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:02:00` <br>（2 分钟） | 为来自 Azure 逻辑应用运行时的表和队列存储请求设置操作超时值（包括重试数）。 |
| `Runtime.ContentStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>（2 秒） | 设置发送到 Blob 存储的重试之间的回退间隔。 |
| `Runtime.ContentStorage.RequestOptionsMaximumAttempts` | `4` 次重试 | 设置发送到表和队列存储的最大重试次数。 |
||||

<a name="store-inline-or-blob"></a>

### <a name="store-content-inline-or-use-blobs"></a>以内联方式存储内容或使用 Blob

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.FlowRunEngine.ForeachMaximumItemsForContentInlining` | `20` 项 | 当 `For each` 循环运行时，每个项的值要么与表存储中的其他元数据以内联方式一起存储，要么单独存储在 Blob 存储中。 设置要与其他元数据以内联方式一起存储的项数。 |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPagesForContentInlining` | `20` 页 | 设置在存储到 Blob 存储之前要作为内联内容存储在表存储中的最大页数。 |
| `Runtime.FlowTriggerSplitOnJob.MaximumItemsForContentInlining` | `40` 项 | 当 `SplitOn` 设置将数组项拆分为多个工作流实例，每个项的值要么与表存储中的其他元数据以内联方式一起存储，要么单独存储在 Blob 存储中。 设置要以内联方式存储的项数。 |
| `Runtime.ScaleUnit.MaximumCharactersForContentInlining` | `8192` 个字符 | 设置在存储到 Blob 存储之前要以内联方式存储在表存储中的最大操作输入和输出字符数。 |
||||

<a name="table-queue-storage"></a>

### <a name="table-and-queue-storage"></a>表和队列存储

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.DataStorage.RequestOptionsServerTimeout` | `00:00:16` <br>（16 秒） | 设置来自 Azure 逻辑应用运行时的表和队列存储请求的超时值。 |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:00:45` <br>（45 秒） | 为来自 Azure 逻辑应用运行时的表和队列存储请求设置操作超时值（包括重试数）。 |
| `Runtime.DataStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>（2 秒） | 设置向表和队列存储发送时的重试之间的回退间隔时间。 |
| `Runtime.DataStorage.RequestOptionsMaximumAttempts` | `4` 次重试 | 设置向表和队列存储发送时的最大重试次数。 |
||||

<a name="retry-policy"></a>

### <a name="retry-policy-for-all-other-operations"></a>所有其他操作的重试策略

| 设置 | 默认值 | 说明 |
|---------|---------------|-------------|
| `Runtime.ScaleMonitor.MaxPollingLatency` | `00:00:30` <br>（30 秒） | 设置运行时缩放的最大轮询延迟。 |
| `Runtime.Backend.Operation.MaximumRetryCount` | `90` 次重试 | 设置工作流操作的重试策略定义中的最大重试次数。 |
| `Runtime.Backend.Operation.MaximumRetryInterval` | `01:00:00:01` <br>（1 天 1 秒） | 设置工作流操作的重试策略定义中的最大间隔。 |
| `Runtime.Backend.Operation.MinimumRetryInterval` | `00:00:05` <br>（5 秒） | 设置工作流操作的重试策略定义中的最小间隔。 |
||||

<a name="manage-host-settings"></a>

## <a name="manage-host-settings---hostjson"></a>管理主机设置 - host.json

可以添加、更新或删除主机设置，这些设置指定将用于该逻辑应用中所有工作流的运行时配置设置和值，例如吞吐量、容量、数据大小等的默认值，无论它们是在本地运行还是 Azure 中运行 。 有关特定于逻辑应用的主机设置，请查看[可用运行时和部署设置的参考指南 - host.json](#reference-host-json)。

### <a name="visual-studio-code---hostjson"></a>Visual Studio Code - host.json

若要在 Visual Studio Code 中查看逻辑应用的主机设置，请执行以下步骤：

1. 在逻辑应用项目中的根项目级别，找到并打开 host.json 文件。

1. 在 `extensions` 对象中的 `workflows` 和 `settings` 下，查看以前为逻辑应用添加的所有主机设置。 否则 `extensions` 对象不会出现在文件中。

   有关主机设置的详细信息，请查看[可用主机设置的参考指南 - host.json](#reference-host-json)。

若要添加主机设置，请执行以下步骤：

1. 在 host.json 文件中的 `extensionBundle` 对象下，添加包含 `workflow` 和 `settings` 对象的 `extensions` 对象，例如：

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. 在 `settings` 对象中，添加一个包含要用于逻辑应用中所有工作流的主机设置的简单列表，无论这些工作流是在本地运行还是 Azure 中运行，例如：

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

### <a name="azure-portal---hostjson"></a>Azure 门户 - host.json

若要在 Azure 门户中查看基于单租户的逻辑应用的主机设置，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)搜索框中，找到并打开你的逻辑应用。

1. 在逻辑应用菜单上的“开发工具”下，选择“高级工具”。

1. 在“高级工具”窗格上，选择“转到”，这将打开逻辑应用的 Kudu 环境  。

1. 在 Kudu 工具栏上，打开“调试控制台”菜单，然后选择“CMD” 。

1. 在 Azure 门户中，停止你的逻辑应用。

   1. 在逻辑应用菜单中，选择“概述”。

   1. 在“概述”窗格的工具栏上，选择“停止”。

1. 在逻辑应用菜单上的“开发工具”下，选择“高级工具”。

1. 在“高级工具”窗格上，选择“转到”，这将为你的逻辑应用打开 Kudu 环境。

1. 在 Kudu 工具栏上，打开“调试控制台”菜单，并选择“CMD”。

   此时将打开一个控制台窗口，以便你可使用命令提示符浏览到 wwwroot 文件夹。 另外，你可以浏览控制台窗口上方显示的目录结构。

1. 沿着以下路径浏览到 wwwroot 文件夹：`...\home\site\wwwroot`。

1. 在控制台窗口上方的目录表中，选择 host.json 文件旁边的“编辑” 。

1. 打开 host.json 后，查看之前为逻辑应用添加的所有主机设置。

   有关主机设置的详细信息，请查看[可用主机设置的参考指南 - host.json](#reference-host-json)。

若要添加设置，请执行以下步骤：

1. 在添加或编辑设置之前，请在 Azure 门户中停止逻辑应用。

   1. 在逻辑应用菜单中，选择“概述”。
   1. 在“概述”窗格的工具栏上，选择“停止”。

1. 返回到 host.json 文件。 在 `extensionBundle` 对象下，添加包含 `workflow` 和 `settings` 对象的 `extensions` 对象，例如：

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. 在 `settings` 对象中，添加一个包含要用于逻辑应用中所有工作流的主机设置的简单列表，无论这些工作流是在本地运行还是 Azure 中运行，例如：

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

1. 完成后，记得选择“保存”。

1. 现在，重启逻辑应用。 返回到逻辑应用的“概述”页，然后选择“重启” 。

---

## <a name="next-steps"></a>后续步骤

- [为在不同单租户 Azure 逻辑应用环境的工作流中会有所不同的值创建参数](parameterize-workflow-app.md)
- [为单租户 Azure 逻辑应用设置 DevOps 部署](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
