---
title: 内置触发器和操作
description: 使用内置触发器和操作创建集成应用、数据、服务和系统的自动工作流，以控制工作流并使用 Azure 逻辑应用管理数据。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 83800e088599bca0023d734bba52b6ed3207f0a3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515337"
---
# <a name="built-in-triggers-and-actions-in-azure-logic-apps"></a>Azure 逻辑应用中的内置触发器和操作

通过[内置触发器和操作](apis-list.md)，你可以执行以下操作：[控制工作流的计划和结构](#control-workflow)、[运行自己代码](#run-code-from-workflows)、[管理或操作数据](#manage-or-manipulate-data)以及完成工作流中的其他任务。 不同于[托管连接器](managed.md)，许多内置操作没有与特定服务、系统或协议相关联。 例如，你可以使用“定期”触发器启动计划上的几乎任何工作流。 或者可以使用“请求”触发器，使工作流在被调用以前都保持等待状态。 所有内置操作都在 Azure 逻辑应用中以本机方式运行，并且大多数都不要求在使用前创建连接。

对于更少数量的服务、系统和协议，Azure 逻辑应用提供内置操作，如 Azure 应用服务、Azure Functions、Azure API 管理，并且用于调用其他 Azure 逻辑应用逻辑应用工作流。 根据创建的是在多租户 Azure 逻辑应用中运行的基于消耗计划的逻辑应用资源，还是在单租户 Azure 逻辑应用中运行的基于标准计划的逻辑应用资源，可用的数量和范围有所不同。 有关详细信息，请参阅[单租户与多租户以及集成服务环境 (ISE)](../logic-apps/single-tenant-overview-compare.md)。 在大多数情况下，内置版本提供更好的性能、更全面的功能以及更实惠的价格。

例如，如果创建单租户逻辑应用，则内置操作和[托管连接器操作](managed.md)都适用于一些服务，尤其是 Azure 服务总线、Azure 事件中心、SQL Server、DB2 和 MQ。 在少数情况下，内置操作仅在一个环境中可用，例如，平面文件目前仅在多租户中可用，而 IBM 主机文件仅在单租户中可用。 在大多数情况下，内置版本提供更好的性能、更全面的功能以及更实惠的价格。

以下列表仅介绍了部分可通过[内置触发器和操作](#general-built-in-triggers-and-actions)完成的任务：

- 使用自定义计划和高级计划运行工作流。 有关计划的详细信息，请查看 [Azure 逻辑应用连接器概述中的定期行为部分](apis-list.md#recurrence-behavior)。

- 组织和控制工作流的结构（例如使用循环和条件执行此操作）。

- 利用变量、日期、数据操作、内容转换和批处理操作。

- 使用 HTTP 触发器和操作与其他终结点通信。

- 接收和响应请求。

- 调用自己的函数 (Azure Functions)、Web 应用（Azure 应用服务）、API（Azure API 管理）以及其他可以接收请求的 Azure 逻辑应用工作流等。

## <a name="general-built-in-triggers-and-actions"></a>常规内置触发器和操作

Azure 逻辑应用提供以下内置触发器和操作：

:::row:::
    :::column:::
        [![日程安排图标][schedule-icon]][schedule-doc]
        \
        \
        [计划][schedule-doc]
        \
        \
        [**定期**][schedule-recurrence-doc]：根据指定重复周期触发工作流。
        \
        \
        [**滑动窗口**][schedule-sliding-window-doc]：触发需要处理连续区块中的数据的工作流。
        \
        \
        [**延迟**][schedule-delay-doc]：在指定的持续时间内暂停工作流。
        \
        \
        [**延迟截止时间**][schedule-delay-until-doc]：在指定的日期和时间之前暂停工作流。
    :::column-end:::
    :::column:::
        [![HTTP 触发器和操作图标][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        使用 HTTP 触发器或操作调用 HTTP 或 HTTPS 终结点。
        \
        \
        还可使用以下其他内置 HTTP 触发器和操作：
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![请求触发器图标][http-request-icon]][http-request-doc]
        \
        \
        [**请求**][http-request-doc]
        \
        \
        [收到 HTTP 请求时][http-request-doc]：等待来自其他工作流、应用或服务的请求。 此触发器使工作流可调用，而无需根据计划进行检查或轮询。
        \
        \
        [**响应**][http-request-doc]：响应由同一工作流中的“收到 HTTP 请求时”触发器接收的请求。
    :::column-end:::
    :::column:::
        [![批处理图标][batch-icon]][batch-doc]
        \
        \
        [**批处理**][batch-doc]
        \
        \
        [**批处理消息**][batch-doc]：触发批量处理消息的工作流。
        \
        \
        [**发送要批量处理的消息**][batch-doc]：调用当前以“批处理消息”触发器开头的现有工作流。
    :::column-end:::
:::row-end:::

## <a name="service-based-built-in-trigger-and-actions"></a>基于服务的内置触发器和操作

Azure 逻辑应用为以下服务提供下列内置操作：

:::row:::
    :::column:::
        [![Azure API 管理图标][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API 管理**][azure-api-management-doc]
        \
        \
        使用 [Azure API 管理](../api-management/api-management-key-concepts.md)在你定义、管理和发布的 API 中调用自己的触发器和操作。 <p><p>**注意**：使用 [API 管理的消耗层](../api-management/api-management-features.md)时不受支持。
    :::column-end:::
    :::column:::
        [![Azure 应用服务图标][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [Azure 应用服务][azure-app-services-doc]
        \
        \
        调用在 [Azure 应用服务](../app-service/overview.md)上创建和托管的应用，如 API 应用和 Web 应用。
        \
        \
        包含 Swagger 后，由这些应用定义的触发器和操作将与 Azure 逻辑应用中的其他任何第一类触发器和操作类似。
    :::column-end:::
    :::column:::
        [![Azure 逻辑应用图标][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure 逻辑应用**][nested-logic-app-doc]
        \
        \
        调用其他以名为“收到 HTTP 请求时”的“请求”触发器开头的工作流。
    :::column-end:::
    :::column:::
        [![SQL Server 图标][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc] <br>（仅限单租户）    \
        \
        连接到本地 SQL Server 或云中的 Azure SQL 数据库，以便可以管理记录、运行存储过程或执行查询。 <p>**注意**：单租户 Azure 逻辑应用提供 SQL 内置和托管连接器操作，而多租户 Azure 逻辑应用仅提供托管连接器操作。 <p>有关详细信息，请参阅[适用于 Azure 逻辑应用的单租户与多租户和集成服务环境](../logic-apps/single-tenant-overview-compare.md)。
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>从工作流运行代码

Azure 逻辑应用提供用于在工作流中运行自己的代码的以下内置操作：

:::row:::
    :::column:::
        [![Azure Functions 图标][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        调用 [Azure 托管的函数](../azure-functions/functions-overview.md)，在工作流中运行自己的代码片段（C# 或 Node.js）。
    :::column-end:::
    :::column:::
        [![内联代码操作图标][inline-code-icon]][inline-code-doc]
        \
        \
        [**内联代码**][inline-code-doc]
        \
        \
        [**执行 JavaScript 代码**][inline-code-doc]：在工作流中添加并运行自己的内联 JavaScript 代码片段。
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>控制工作流

Azure 逻辑应用提供用于在工作流中构建和控制操作的以下内置操作：

:::row:::
    :::column:::
        [![条件操作图标][condition-icon]][condition-doc]
        \
        \
        [**条件**][condition-doc]
        \
        \
        评估条件，并根据条件是 true 还是 false 运行不同的操作。
    :::column-end:::
    :::column:::
        [![针对每个操作图标][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        对数组中的每个项执行相同的操作。
    :::column-end:::
    :::column:::
        [![范围操作图标][scope-icon]][scope-doc]
        \
        \
        [**名称**][scope-doc]
        \
        \
        将操作分组到范围，以便在该范围内的操作完成运行后，获取这些操作的自身状态。
    :::column-end:::
    :::column:::
        [![切换操作图标][switch-icon]][switch-doc]
        \
        \
        [**切换**][switch-doc]
        \
        \
        将操作分组到案例，而案例分配有唯一的值（默认案例除外）。 仅运行其分配值与表达式、对象或令牌的结果相匹配的案例。 如果不存在任何匹配项，则运行默认案例。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![终止操作图标][terminate-icon]][terminate-doc]
        \
        \
        [**终止**][terminate-doc]
        \
        \
        停止当前正在运行的逻辑应用工作流。
    :::column-end:::
    :::column:::
        [![截止操作图标][until-icon]][until-doc]
        \
        \
        [**截止**][until-doc]
        \
        \
        重复操作，直到指定的条件为 true 或某个状态发生更改。
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>管理或处理数据

Azure 逻辑应用提供了以下用于处理数据输出及其格式的内置操作：

:::row:::
    :::column:::
        [![数据操作图标][data-operations-icon]][data-operations-doc]
        \
        \
        [**数据操作**][data-operations-doc]
        \
        \
        对数据执行操作。
        \
        \
        **撰写**：基于具有不同类型的多个输入创建单个输出。
        \
        \
        **创建 CSV 表**：基于包含 JSON 对象的数组创建逗号分隔值 (CSV) 表。
        \
        \
        **创建 HTML 表**：基于包含 JSON 对象的数组创建一个 HTML 表。
        \
        \
        **筛选数组**：基于符合条件的另一个数组中的项创建一个数组。
        \
        \
        **联接**：基于数组中的所有项创建一个字符串，并使用指定的分隔符分隔这些项。
        \
        \
        **分析 JSON**：基于 JSON 内容中的属性及其值创建用户友好的令牌，以便可以在工作流中使用这些属性。
        \
        \
        **选择**：通过转换另一数组中的项或值并将这些项映射到指定的属性，创建包含 JSON 对象的数组。
    :::column-end:::
    :::column:::
        ![日期时间操作图标][date-time-icon]
        \
        \
        **日期时间**
        \
        \
        对时间戳执行操作。
        \
        \
        **添加到时间**：将指定的单位数添加到时间戳。
        \
        \
        **转换时区**：将时间戳从源时区转换为目标时区。
        \
        \
        **当前时间**：返回字符串形式的当前时间戳。
        \
        \
        **获取将来的时间**：返回当前时间戳加上指定的时间单位。
        \
        \
        **获取过去的时间**：返回当前时间戳减去指定的时间单位。
        \
        \
        **从时间中减去**：从时间戳中减去一定数目的时间单位。
    :::column-end:::
    :::column:::
        [![变量操作图标][variables-icon]][variables-doc]
        \
        \
        [**变量**][variables-doc]
        \
        \
        对变量执行操作。
        \
        \
        **追加到数组变量**：插入一个值，作为变量存储的数组中的最后一个项。
        \
        \
        **追加到字符串变量**：插入一个值，作为变量存储的字符串中的最后一个字符。
        \
        \
        **递减变量**：按常量值减小变量。
        \
        \
        **递增变量**：按常量值增大变量。
        \
        \
        **初始化变量**：创建一个变量并声明其数据类型和初始值。
        \
        \
        **设置变量**：将不同的值分配给现有变量。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-built-in-actions"></a>集成帐户内置操作

Azure 逻辑应用提供以下内置操作，这些操作要么在使用基于消耗计划的多租户 Azure 逻辑应用时需要集成帐户，要么在使用基于标准计划的单租户 Azure 逻辑应用时不需要集成帐户：

> [!NOTE]
> 必须先[将逻辑应用资源链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，然后才能在基于消耗计划的多租户 Azure 逻辑应用中使用集成帐户操作。 但是，在基于标准计划的单租户 Azure 逻辑应用中，有些集成帐户操作不需要将逻辑应用资源链接到集成帐户，例如 Liquid 操作和 XML 操作。 若要使用 B2B 操作，需要具有 Liquid 映射、XML 映射或 XML 架构；你可在 Azure 门户中通过各自的操作上传这些内容，或者使用各自的“映射”和“架构”文件夹将其添加到 Visual Studio Code 项目的“项目”文件夹中  。

:::row:::
    :::column:::
        [![平面文件解码图标][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**平面文件解码**<br>（*仅限多租户*）][flat-file-decode-doc]
        \
        \
        在将内容发送到参与方之前对 XML 进行编码。
    :::column-end:::
    :::column:::
        [![平面文件编码图标][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**平面文件编码**<br>（*仅限多租户*）][flat-file-encode-doc]
        \
        \
        在收到参与方提供的内容后对 XML 进行解码。
    :::column-end:::
    :::column:::
        [![集成帐户图标][integration-account-icon]][integration-account-doc]
        \
        \
        [**集成帐户项目查找**<br>（*仅限多租户*）][integration-account-doc]
        \
        \
        获取集成帐户中项目的自定义元数据，例如参与方、协议、架构等。
    :::column-end:::
    :::column:::
        [![Liquid 操作图标][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid 操作**][json-liquid-transform-doc]
        \
        \
        使用 Liquid 模板转换以下格式： <p><p>- JSON 到 JSON <br>- JSON 到 TEXT <br>- XML 到 JSON <br>- XML 到 TEXT
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![转换 XML 图标][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**转换 XML**][xml-transform-doc]
        \
        \
        将源 XML 格式转换为另一种 XML 格式。
    :::column-end:::
    :::column:::
        [![XML 验证图标][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML 验证**][xml-validate-doc]
        \
        \
        根据指定的架构验证 XML 文档。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建可从 Azure 逻辑应用调用的自定义 API](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in integration account connector icons -->
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[xml-transform-icon]: ./media/apis-list/xml-transform.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "创建 Azure API 管理服务实例用于管理和发布 API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "将逻辑应用与应用服务 API 应用集成"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 存储连接器管理 Blob 容器中的文件"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "将逻辑应用与 Azure Functions 集成"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "以组或批的形式处理消息"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "评估条件并根据条件是 true 还是 false 运行不同的操作"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "执行数据操作，如筛选数组或创建 CSV 和 HTML 表"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "对数组中的每个项执行相同的操作"
[http-doc]: ./connectors-native-http.md "从逻辑应用调用 HTTP 或 HTTPS 终结点"
[http-request-doc]: ./connectors-native-reqres.md "在逻辑应用中接收 HTTP 请求"
[http-response-doc]: ./connectors-native-reqres.md "响应来自逻辑应用的 HTTP 请求"
[http-swagger-doc]: ./connectors-native-http-swagger.md "从逻辑应用调用 REST 终结点"
[http-webhook-doc]: ./connectors-native-webhook.md "等待来自 HTTP 或 HTTPS 终结点的特定事件"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md从逻辑应用添加和运行 JavaScript 代码片段 ""
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "将逻辑应用与嵌套工作流集成"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "通过查询操作选择和筛选数组"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "按计划运行逻辑应用"
[schedule-delay-doc]: ./connectors-native-delay.md "延迟运行下一操作"
[schedule-delay-until-doc]: ./connectors-native-delay.md "延迟运行下一操作"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "按重复计划运行逻辑应用"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "运行需要处理连续区块中的数据的逻辑应用"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "将操作组织成组，以便在该组中的操作完成运行后获取这些操作的自身状态"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL Server。在 SQL 数据库表中创建、更新、获取和删除条目"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "将操作组织成分配有唯一值的案例。仅运行其值与表达式、对象或令牌的结果相匹配的案例。如果不存在任何匹配项，则运行默认案例"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消逻辑应用的正在运行的工作流"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重复操作，直到指定的条件为 true 或某个状态发生更改"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "使用变量执行操作，例如初始化、设置、递增、递减和追加到字符串或数组变量"

<!--Built-in integration account doc links-->
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "管理集成帐户项目的元数据"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "使用 Liquid 模板转换 JSON"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "转换 XML 消息"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "验证 XML 消息"
