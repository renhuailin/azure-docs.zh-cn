---
title: 使用量计量、计费和定价
description: 了解 Azure 逻辑应用中使用量计量、计费和定价模型的工作原理。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: 63c7a2c79ca5f0d241ddc3727d006bb2befb8163
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860812"
---
# <a name="usage-metering-billing-and-pricing-models-for-azure-logic-apps"></a>适用于 Azure 逻辑应用的使用量计量、计费和定价模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍适用于 Azure 逻辑应用和相关资源的计量、计费和定价模型的工作原理。 有关特定定价费率、成本计划或不同托管环境等信息，请查看以下内容：

* [适用于 Azure 逻辑应用的定价费率](https://azure.microsoft.com/pricing/details/logic-apps)
* [计划和管理 Azure 逻辑应用的成本](plan-manage-costs.md)
* [单租户与多租户和集成服务环境](single-tenant-overview-compare.md)

<a name="consumption-pricing"></a>

## <a name="consumption-multi-tenant"></a>消耗（多租户）

在多租户 Azure 逻辑应用中，逻辑应用及其工作流在定价和计费方面遵循[消耗计划](https://azure.microsoft.com/pricing/details/logic-apps)。 可以通过多种方式创建此类逻辑应用，例如，当选择“逻辑应用(消耗)”资源类型时、使用 Visual Studio Code 中的“Azure 逻辑应用(消耗)”扩展，或者当创建[自动化任务](create-automation-tasks-azure-resources.md)时 。

下表总结了当以下组件与多租户 Azure 逻辑应用中的逻辑应用和工作流一起使用时，消耗模型如何处理这些组件的计量和计费：

| 组件 | 度量和计费 |
| ----------|----------------------|
| 触发器和操作 | 消耗模型包含按 Azure 订阅提供的初始免费内置操作数，工作流可以运行这些内置操作。 当执行的内置操作数超出此数量后，会对每次执行进行计量，并按照[消耗计划中的操作定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计费 。 对于其他操作类型，例如托管连接器，将按照[消耗计划中的标准或企业连接器定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计费 。 有关详细信息，请参阅[消耗模型中的触发器和操作](#consumption-operations)。 |
| 存储操作 | 仅对与数据保留相关的存储消耗进行计量，例如保存工作流运行历史记录中的输入和输出。 计费遵循[适用于消耗计划的数据保留定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 有关详细信息，请参阅[存储操作](#storage-operations)。 |
| 集成帐户 | 根据所创建的并与逻辑应用一起使用的集成帐户类型进行计量。 计费遵循[集成帐户定价](https://azure.microsoft.com/pricing/details/logic-apps/)，除非逻辑应用部署并托管在[集成服务环境 (ISE)](#ise-pricing) 中。 有关详细信息，请参阅[集成帐户](#integration-accounts)。 |
|||

<a name="consumption-operations"></a>

### <a name="trigger-and-action-operations-in-the-consumption-model"></a>消耗模型中的触发器和操作

除开针对每个 Azure 订阅所包含的工作流可以运行的初始免费内置操作数，消耗模型将基于操作的每次执行对操作进行计量和计费，无论整个工作流是否成功运行、完成，甚至是否已实例化。 一个操作通常执行一次，[除非操作启用了重试尝试](#other-operation-behavior)。 相应地，一次执行通常进行一次调用，[除非操作支持并启用了分块或分页以获取大量数据](logic-apps-handle-large-messages.md)。 如果启用了分块或分页，一次操作执行可能需要进行多次调用。 消耗模型按执行对操作进行计量和计费，而不是按调用来对操作进行计量和计费。

例如，假设一个工作流从轮询触发器开始，该触发器通过定期对终结点进行出站调用来获取记录。 出站调用会作为单次执行来计量和计费，无论触发器是否触发或被跳过。 触发器状态控制是否已创建和运行工作流实例。 现在，假设此操作还支持并启用了分块或分页。 如果此操作必须执行 10 次调用才能完成所有数据的获取，尽管进行了多次调用，但仍会将此操作作为单次执行来进行计量和计费。

下表总结了当以下操作类型与多租户 Azure 逻辑应用中的逻辑应用和工作流一起使用时，消耗模型如何处理这些操作类型的计量和计费：

| 操作类型 | 说明 | 度量和计费 |
|----------------|-------------|----------------------|
| [*内置*](../connectors/built-in.md) | 这些操作使用 Azure 逻辑应用运行时直接本机运行。 在设计器中，你可以在“内置”标签下找到这些操作。 <p>例如，HTTP 触发器和请求触发器是内置触发器。 HTTP 操作和响应操作是内置操作。 其他内置操作包括工作流控制操作（例如循环和条件）、数据操作和批处理操作等。 | 消耗模型包含按 Azure 订阅提供的初始免费内置操作数，工作流可以运行这些内置操作。 当执行的内置操作数超出此数量后，将按照[操作定价](https://azure.microsoft.com/pricing/details/logic-apps/)来对内置操作的执行进行计费。 <p><p>注意：某些托管连接器操作也作为内置操作提供，这些操作的执行包含在初始免费操作数中。 当执行的这些操作数超出初始免费操作数后，将按照[操作定价](https://azure.microsoft.com/pricing/details/logic-apps/)来对这些操作进行计费，而不是按照[标准或企业连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/)来进行计费  。 |
| [托管连接器](../connectors/managed.md) | 这些操作在 Azure 中单独运行。 在设计器中，你可以在“标准”或“企业”标签下找到这些操作 。 | 这些操作的执行遵循[标准或企业连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/) 。 <p><p>注意：预览版企业连接器操作的执行遵循[消耗计划中的标准连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 |
| [*自定义连接器*](../connectors/apis-list.md#custom-apis-and-connectors) | 这些操作在 Azure 中单独运行。 在设计器中，你可以在“自定义”标签下找到这些操作。 有关连接器数量、吞吐量和超时的限制，请参阅 [Azure 逻辑应用中的自定义连接器限制](logic-apps-limits-and-config.md#custom-connector-limits)。 | 这些操作的执行遵循[标准连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 |
||||

有关消耗模型如何处理在其他操作（例如循环）内运行的操作、处理多个项目（例如数组）的操作以及重试策略的操作的详细信息，请参阅[其他操作行为](#other-operation-behavior)。

<a name="consumption-cost-estimation-tips"></a>

### <a name="cost-estimation-tips-for-the-consumption-model"></a>消耗模型的成本估算提示

以下提示有助于更准确地估计消耗成本：

* 请考虑任何给定日可能到达的消息或事件数，而不是仅基于轮询间隔进行计算。

* 当某个事件或消息满足触发器条件时，许多触发器将立即尝试读取任何其他满足条件的等待事件或消息。 此行为意味着，即使你选择较长的轮询间隔，触发器也基于符合启动工作流条件的等待事件或消息的数量进行触发。 遵循此行为的触发器包括 Azure 服务总线和 Azure 事件中心。

  例如，假设你设置了一个每天检查终结点的触发器。 当触发器检查终结点并找到 15 个满足条件的事件时，触发器触发并运行相应工作流 15 次。 逻辑应用服务会计量这 15 个工作流执行的所有操作，包括触发器请求。

<a name="standard-pricing"></a>

## <a name="standard-single-tenant"></a>标准（单租户）

在单租户 Azure 逻辑应用中，逻辑应用及其工作流在定价和计费方面遵循[标准计划](https://azure.microsoft.com/pricing/details/logic-apps/)。 可以通过多种方式创建此类逻辑应用，例如，当选择“逻辑应用(标准)”资源类型时或者使用 Visual Studio Code 中的“Azure 逻辑应用(标准)”扩展 。 此定价模型要求逻辑应用使用托管计划和定价层，与消耗计划的不同之处在于，你需要为预留容量和专用资源付费，无论你是否使用了它们。

> [!IMPORTANT]
> 基于“逻辑应用(标准)”资源类型创建或部署新的逻辑应用时，可以在所有 Azure 区域中使用工作流标准托管计划，或者可以使用应用服务托管计划，但前提是在“基本信息”选项卡上选择了“应用服务环境 v3”区域  。
>
> 尽管预览版“逻辑应用(标准)”资源类型使你可以使用应用服务计划、Functions 高级计划、应用服务环境 v1 和应用服务环境 v2，但对于此 Azure 逻辑应用资源类型的公开发布版，这些选项将不再可用或不再受支持。

下表总结了当以下组件与单租户 Azure 逻辑应用中的逻辑应用和工作流一起使用时，标准模型如何处理这些组件的计量和计费：

| 组件 | 度量和计费 |
|-----------|----------------------|
| 虚拟 CPU (vCPU) 和内存 | 标准模型要求逻辑应用使用工作流标准托管计划和定价层，这确定了应用于计算和内存容量的资源级别和定价费率。 有关详细信息，请参阅[标准模型中的定价层](#standard-pricing-tiers)。 |
| 触发器和操作 | 标准模型包含无限的免费内置操作数，工作流可以运行这些内置操作。 <p>如果工作流使用任何托管连接器操作，将根据每次调用来对这些操作进行计量，同时将按照[与消耗计划相同的标准或企业连接器定价](https://azure.microsoft.com/pricing/details/logic-apps)来进行计费  。 有关详细信息，请参阅[标准模型中的触发器和操作](#standard-operations)。 |
| 存储操作 | 将对由 Azure 逻辑应用运行的任何存储操作进行计量。 例如，当服务保存工作流运行历史记录中的输入和输出时，就会运行存储操作。 计费将遵循所选的[定价层](#standard-pricing-tiers)。 有关详细信息，请参阅[存储操作](#storage-operations)。 |
| 集成帐户 | 如果创建集成帐户以供逻辑应用使用，将基于所创建的集成帐户类型进行计量。 计费将遵循[集成帐户定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 有关详细信息，请参阅[集成帐户](#integration-accounts)。 |
|||

<a name="standard-pricing-tiers"></a>

### <a name="pricing-tiers-in-the-standard-model"></a>标准模型中的定价层

所选用于为逻辑应用进行计量和计费的定价层包含虚拟 CPU (vCPU) 和内存资源的特定计算量。 目前，只有工作流标准托管计划可用于“逻辑应用(标准)”资源类型，并提供以下定价层 ：

| 定价层 | 虚拟 CPU (vCPU) | 内存 (GB) |
|--------------|--------------------|-------------|
| **WS1** | 1 | 3.5 |
| **WS2** | 2 | 7 |
| **WS3** | 4 | 14 |
||||

> [!IMPORTANT]
>
> 以下示例仅用于演示，提供了示例估计来大致展示定价层的工作原理。 
> 有关基于 Azure 逻辑应用可用的特定区域的特定 vCPU 和内存定价，请参阅 [Azure 逻辑应用定价页上所选区域的标准计划](https://azure.microsoft.com/pricing/details/logic-apps/)。
>
> 假设在示例区域中，以下资源每小时的费率为：
>
> | 资源 | 每小时费率（示例区域） |
> |----------|-----------------------------|
> | **vCPU** | 每 vCPU 0.192 美元 |
> | **内存** | 每 GB 0.0137 美元 |
> |||
>
> 以下计算提供预估的每月费率：
>
> <每月费率> = 730 小时（每月）* [（<vCPU 数> * <vCPU 每小时费率>）+（<内存 GB 数> * <GB 内存每小时费率>）]    
>
> 根据上述信息，下表列出了每个定价层以及该定价层中资源的预估每月费率：
>
> | 定价层 | 虚拟 CPU (vCPU) | 内存 (GB) | 每月费率（示例区域） |
> |--------------|--------------------|-------------|------------------------------|
> | **WS1** | 1 | 3.5 | 175.16 美元 |
> | **WS2** | 2 | 7 | 350.33 美元 |
> | **WS3** | 4 | 14 | 700.65 美元 |
> |||||

<a name="standard-operations"></a>

### <a name="trigger-and-action-operations-in-the-standard-model"></a>标准模型中的触发器和操作

除开工作流可以运行的无限数量的免费内置操作，标准模型将基于操作的每次调用对操作进行计量和计费，无论整个工作流是否成功运行、完成，甚至是否已实例化。 一个操作通常执行一次，[除非操作启用了重试尝试](#other-operation-behavior)。 相应地，一次执行通常进行一次调用，[除非操作支持并启用了分块或分页以获取大量数据](logic-apps-handle-large-messages.md)。 如果启用了分块或分页，一次操作执行可能需要进行多次调用。 标准模型按调用对操作进行计量和计费，而不是按执行来对操作进行计量和计费。

例如，假设一个工作流从轮询触发器开始，该触发器通过定期对终结点进行出站调用来获取记录。 出站调用会被计量和计费，无论触发器是否触发或被跳过。 触发器状态控制是否已创建和运行工作流实例。 现在，假设此操作还支持并启用了分块或分页。 如果此操作必须执行 10 次调用才能完成所有数据的获取，那么将按调用来对此操作进行计量和计费。

下表总结了当以下操作类型与单租户 Azure 逻辑应用中的逻辑应用和工作流一起使用时，标准模型如何处理这些操作类型的计量和计费：

| 操作类型 | 说明 | 度量和计费 |
|----------------|-------------|----------------------|
| [*内置*](../connectors/built-in.md) | 这些操作使用 Azure 逻辑应用运行时直接本机运行。 在设计器中，你可以在“内置”标签下找到这些操作。 <p>例如，HTTP 触发器和请求触发器是内置触发器。 HTTP 操作和响应操作是内置操作。 其他内置操作包括工作流控制操作（例如循环和条件）、数据操作和批处理操作等。 | 标准模型包含无限的免费内置操作数。 <p><p>注意：某些托管连接器操作也作为内置操作提供。 尽管内置操作是免费的，但标准模型仍会使用[与消耗模型相同的标准或企业连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/)来对托管连接器操作进行计量和计费 。 |
| [托管连接器](../connectors/managed.md) | 这些操作在 Azure 中单独运行。 在设计器中，你可以在组合的“Azure”标签下找到这些操作。 | 标准模型按照[与消耗模型相同的标准或企业连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/)来对托管连接器操作进行计量和计费 。 <p><p>注意：预览版企业连接器操作遵循[消耗计划中的标准连接器定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 |
| [*自定义连接器*](../connectors/apis-list.md#custom-apis-and-connectors) | 目前，在基于单租户的逻辑应用工作流中只能创建并使用[自定义内置连接器操作](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)。 | 标准模型包含无限的免费内置操作数。 有关吞吐量和超时的限制，请参阅 [Azure 逻辑应用中的自定义连接器限制](logic-apps-limits-and-config.md#custom-connector-limits)。 |
||||

有关标准模型如何处理在其他操作（例如循环）内运行的操作、处理多个项目（例如数组）的操作以及重试策略的操作的详细信息，请参阅[其他操作行为](#other-operation-behavior)。

<a name="ise-pricing"></a>

## <a name="integration-service-environment-ise"></a>集成服务环境 (ISE)

使用“逻辑应用(消耗)”资源类型创建逻辑应用并部署到专用[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 时，逻辑应用及其工作流在定价和计费方面遵循[集成服务环境计划](https://azure.microsoft.com/pricing/details/logic-apps)。 此定价模型取决于 [ISE 级别或 SKU](connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)，与消耗计划的不同之处在于，你需要为预留容量和专用资源付费，无论你是否使用了它们。

下表总结了 ISE 模型如何根据 ISE 级别或 SKU 处理容量和其他专用资源的计量和计费：

| ISE SKU | 度量和计费 |
|---------|----------------------|
| **高级** | 基本单元具有[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)，并且[对于高级 SKU，按每小时费率计费](https://azure.microsoft.com/pricing/details/logic-apps)。 如果需要更多的吞吐量，可以在创建 ISE 时或之后[添加更多缩放单元](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 每个缩放单元按[每小时费率计费，该费率大致为基本单元费率的一半](https://azure.microsoft.com/pricing/details/logic-apps)。 <p><p>有关容量和限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。 |
| **开发人员** | 基本单元具有[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)，并且[对于开发人员 SKU，按每小时费率计费](https://azure.microsoft.com/pricing/details/logic-apps)。 但是，此 SKU 在回收期间没有服务级别协议 (SLA)、纵向扩展功能或冗余，这意味着你可能会遇到延迟或停机。 后端更新可能会间歇性地中断服务。 <p><p>重要说明：确保将此 SKU 仅用于探索、试验、开发或测试，而不用于生产和性能测试。 <p><p>有关容量和限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。 |
|||

下表总结了当以下组件与 ISE 中的逻辑应用和工作流一起使用时，ISE 模型如何处理这些组件：

| 组件 | 说明 |
|-----------|-------------|
| 触发器和操作 | ISE 模型包含免费的内置托管连接器和自定义连接器操作，工作流可以运行这些操作，但这些操作受 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)和 [Azure 逻辑应用中的自定义连接器限制](logic-apps-limits-and-config.md#custom-connector-limits)的约束。 有关详细信息，请参阅 [ISE 模型中的触发器和操作](#integration-service-environment-operations)。 |
| 存储操作 | ISE 模型包含免费的存储消耗，例如数据保留。 有关详细信息，请参阅[存储操作](#storage-operations)。 |
| 集成帐户 | ISE 模型包含一个基于所选 ISE SKU 的免费集成帐户层。 可以支付[额外的费用](https://azure.microsoft.com/pricing/details/logic-apps/)为 ISE 创建更多的集成帐户，最多可达到 [ISE 总限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 有关详细信息，请参阅[集成帐户](#integration-accounts)。 |
|||

<a name="integration-service-environment-operations"></a>

### <a name="trigger-and-action-operations-in-the-ise-model"></a>ISE 模型中的触发器和操作

下表总结了当以下操作类型与 ISE 中的逻辑应用和工作流一起使用时，ISE 模型如何处理这些操作类型：

| 操作类型 | 说明 | 度量和计费 |
|----------------|-------------|----------------------|
| [*内置*](../connectors/built-in.md) | 这些操作使用 Azure 逻辑应用运行时直接本机运行，并且运行在与逻辑应用工作流相同的 ISE 中。 在设计器中，你可以在“内置”标签下找到这些操作，但每个操作也会显示“核心”标签 。 <p>例如，HTTP 触发器和请求触发器是内置触发器。 HTTP 操作和响应操作是内置操作。 其他内置操作包括工作流控制操作（例如循环和条件）、数据操作和批处理操作等。 | ISE 模型免费包含这些操作，但这些操作受 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)的约束。 |
| [托管连接器](../connectors/managed.md) | 无论是标准连接器还是企业连接器，托管连接器操作都在 ISE 或多租户 Azure 中运行，具体取决于连接器或操作是否显示有“ISE”标签 。 <p><p>- ISE 标签：这些操作在与逻辑应用相同的 ISE 中运行，并且无需[本地数据网关](#data-gateway)即可运行。 <p><p>- 无 ISE 标签：这些操作在多租户 Azure 中运行。 | ISE 模型免费包含带 ISE 和不带 ISE 标签的操作，但这些操作受 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)的约束 。 |
| [*自定义连接器*](../connectors/apis-list.md#custom-apis-and-connectors) | 在设计器中，你可以在“自定义”标签下找到这些操作。 | ISE 模型免费包含这些操作，但这些操作受 [Azure 逻辑应用中的自定义连接器限制](logic-apps-limits-and-config.md#custom-connector-limits)的约束。 |
||||

有关 ISE 模型如何处理在其他操作（例如循环）内运行的操作、处理多个项目（例如数组）的操作以及重试策略的操作的详细信息，请参阅[其他操作行为](#other-operation-behavior)。

<a name="other-operation-behavior"></a>

## <a name="other-operation-behavior"></a>其他操作行为

下表总结了消耗、标准和 ISE 模型如何处理在其他操作（例如循环）内运行的操作、处理多个项目（例如数组）的操作以及重试策略的操作：

| 操作 | 说明 | 消耗 | Standard | ISE |
|-----------|-------------|-------------|----------|-----|
| [循环操作](logic-apps-control-flow-loops.md) | 循环操作（例如 For each 或 Until 循环）可以包含在每个循环周期内运行的其他操作 。 | 除开所包含的初始内置操作数，每次循环运行时，都会对循环操作以及此循环中的每个操作进行计量。 如果某一操作处理集合中的任何项，例如列表或数组，那么这些项的数量也会用于计量计算中。 <p><p>例如，假设有一个 For each 循环，其中包含处理一个列表的操作。 服务会将列表项的数量与循环中的操作数量相乘，再加上启动循环的操作数。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。 <p><p>定价取决于操作类型是内置操作、标准连接器操作还是企业连接器操作。 | 除开所含的内置操作数，此类操作的处理方式与消耗模型相同。 | 不对此类操作进行计量或计费。 |
| [重试策略](logic-apps-exception-handling.md#retry-policies) | 针对受支持的操作，可以通过设置[重试策略](logic-apps-exception-handling.md#retry-policies)来实现基本的异常和错误处理。 | 除开初始的内置操作数，将对原始的操作执行以及每次重试的执行进行计量。 例如，对于一个执行 5 次重试的操作，将按 6 次执行来对此操作进行计量和计费。 <p><p>定价取决于操作类型是内置操作、标准连接器操作还是企业连接器操作。 | 除开所含的内置操作数，此类操作的处理方式与消耗模型相同。 | 不对此类操作进行计量或计费。 |
||||||

<a name="storage-operations"></a>

## <a name="storage-operations"></a>存储操作

Azure 逻辑应用将 [Azure 存储](../storage/index.yml)用于任何所需的存储事务，例如使用队列来调度触发器操作或使用表和 blob 来存储工作流状态。 根据工作流中的操作，存储成本会有所不同，因为不同的触发器、操作和有效负载会产生不同的存储操作和需求。 服务还会根据逻辑应用资源的[运行历史记录保留限制](logic-apps-limits-and-config.md#run-duration-retention-limits)来保存和存储工作流运行历史记录中的输入和输出。 可以在逻辑应用资源级别（而不是工作流级别）管理此保留限制。

下表总结了消耗、标准和 ISE 模型如何处理存储操作的计量和计费：

| 型号 | 说明 | 度量和计费 |
|-------|-------------|----------------------|
| 消耗（多租户） | 存储资源和使用量会附加到逻辑应用资源上。 | 按照[适用于消耗计划的数据保留定价](https://azure.microsoft.com/pricing/details/logic-apps)仅对与数据保留相关的存储消耗进行计量和计费。 |
| 标准（单租户） | 可以使用自己的 Azure [存储帐户](../azure-functions/storage-considerations.md#storage-account-requirements)，这使你能够更灵活地对工作流的数据进行更好的控制。 |  计量和计费将遵循 [Azure 存储定价模型](https://azure.microsoft.com/pricing/details/storage/)。 存储成本在 Azure 计费发票中单独列出。 <p><p>提示：为了帮助你更好地了解工作流可以运行的存储操作的数量及成本，请尝试使用[逻辑应用存储计算器](https://logicapps.azure.com/calculator)。 可以选择示例工作流或使用现有的工作流定义。 第一个计算估计工作流中的存储操作数。 然后，可以通过 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)使用这些数字来估算可能的成本。 有关详细信息，请参阅[估算单租户 Azure 逻辑应用中工作流的存储需求和成本](estimate-storage-costs.md)。 |
| 集成服务环境 (ISE) | 存储资源和使用量会附加到逻辑应用资源上。 | 不对此类操作进行计量或计费。 |
||||

有关详细信息，请查看以下文档：

* [查看执行和存储使用情况的指标](plan-manage-costs.md#monitor-billing-metrics)
* [Azure 逻辑应用中的限制](logic-apps-limits-and-config.md)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>本地数据网关

[本地数据网关](../logic-apps/logic-apps-gateway-install.md)是所创建的单独的 Azure 资源，使逻辑应用工作流可以使用特定网关支持的连接器访问本地数据。 网关资源本身不会产生费用，但通过网关运行的操作会产生费用，具体取决于逻辑应用使用的定价和计费模型。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)是一个单独的 Azure 资源，可以将它创建为容器来定义和存储企业到企业 (B2B) 项目，例如贸易合作伙伴、协议、架构和映射等。 创建此帐户并定义这些项目后，将此帐户链接到逻辑应用，使你可以在工作流中使用这些项目和各种 B2B 操作来探索、构建和测试使用 [EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 处理](logic-apps-enterprise-integration-xml.md)功能的集成解决方案。

下表总结了消耗、标准和 ISE 模型如何处理集成帐户的计量和计费：

| 建模 | 度量和计费 |
|-------|----------------------|
| 消耗（多租户） | 计量和计费根据所使用的帐户层使用[集成帐户定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 |
| 标准（单租户） | 计量和计费根据所使用的帐户层使用[集成帐户定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 |
| ISE | 此模型包含单个集成帐户（基于 ISE SKU）。 可以支付[额外的费用](https://azure.microsoft.com/pricing/details/logic-apps/)为 ISE 创建更多的集成帐户，最多可达到 [ISE 总限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 |
|||

有关详细信息，请查看以下文档：

* [创建并管理集成帐户](logic-apps-enterprise-integration-create-integration-account.md)
* [Azure 逻辑应用中的集成帐户限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="other-items-not-metered-or-billed"></a>其他不计量或计费的项

在所有定价模型中，不会对以下项进行计量或计费：

* 由于工作流在完成前停止而未运行的操作
* 禁用的逻辑应用程序或工作流，因为它们在处于非活动状态时不会创建新实例。

## <a name="next-steps"></a>后续步骤

* [计划和管理 Azure 逻辑应用的成本](plan-manage-costs.md)
