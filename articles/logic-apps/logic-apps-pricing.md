---
title: 计费和定价模型
description: 有关 Azure 逻辑应用中定价和计费模型工作原理的概述性介绍
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 3b627abdf27907a5c0739e6dd7920932a3035ee7
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111981690"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Azure 逻辑应用的定价和计费模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍适用于 Azure 逻辑应用和相关资源的计费和定价模型的工作原理。 有关特定定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。 若要了解如何计划、管理和监视成本，请参阅[计划和管理 Azure 逻辑应用的成本](plan-manage-costs.md)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-multi-tenant"></a>使用量定价（多租户）

即用即付的消耗量定价模型适用于在“全局性”的公共多租户 Azure 逻辑应用环境中运行的逻辑应用。 可以使用多个选项创建这些逻辑应用，例如：

* Azure 门户中的逻辑应用（消耗版）资源类型
* Visual Studio Code 中的 Azure 逻辑应用（消耗版）扩展
* Visual Studio 中的 Azure 逻辑应用工具扩展
* 使用 `Microsoft.Logic` 资源类型的 Azure 资源管理器模板（ARM 模板）
* 适用于 Azure 逻辑应用的 Azure CLI（使用 `az logic` 命令）
* 适用于 Azure 逻辑应用的 Azure PowerShell（使用 `Az.LogicApp` 模块）
* 适用于 Azure 逻辑应用的 REST API
* Azure 门户中的[自动化任务](create-automation-tasks-azure-resources.md)

计量和计费基于逻辑应用工作流中的触发器和操作执行。 无论工作流是否成功运行或者甚至是否已实例化，都会对此类执行进行计量和计费。 例如，假设你的自动化任务使用轮询触发器，该触发器定期对终结点进行传出调用。 无论是否激发或跳过触发器，此出站请求都会作为执行进行计量和计费，这会影响是否创建工作流实例。

| 项 | 描述 |
|-------|-------------|
| [内置](../connectors/built-in.md)的触发器和操作 | 在逻辑应用服务中以本机方式运行，并使用[操作价格](https://azure.microsoft.com/pricing/details/logic-apps/)进行计量。 <p><p>例如，HTTP 触发器和请求触发器是内置触发器，HTTP 操作和响应操作是内置操作。 数据操作、批处理操作、变量操作和[工作流控制操作](../connectors/built-in.md)（如循环、条件、开关、并行分支等）也是内置操作。 <p><p>注意：作为每月的额外奖励，消耗计划包含数千次免费的内置执行。 |
| [标准连接器](../connectors/managed.md)触发器和操作 <p><p>[自定义连接器](../connectors/apis-list.md#custom-apis-and-connectors)触发器和操作 | 使用[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps/)进行计量。 |
| [企业连接器](../connectors/managed.md)触发器和操作 | 使用[企业连接器价格](https://azure.microsoft.com/pricing/details/logic-apps/)进行计量。 但是，在连接器预览版期间，企业连接器使用[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps/)进行计量。 |
| [循环](logic-apps-control-flow-loops.md)内的操作 | 循环中运行的每个操作都按运行的每个循环周期计费。 <p><p>例如，假设你有一个“for each”循环，其中包含列表处理操作。 逻辑应用服务通过将列表项的数量乘以循环中的操作数来计量该循环中运行的操作，并加上启动循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。 |
| 重试次数 | 若要处理最基本的异常和错误，可以在支持的情况下为触发器和操作设置[重试策略](logic-apps-exception-handling.md#retry-policies)。 这些重试和原始请求将根据触发器或操作是具有内置类型、标准类型还是企业类型，按一定费率收费。 例如，发生了 2 次重试的操作要按 3 次操作执行来付费。 |
| [数据保留和存储使用情况](#data-retention) | 使用数据保留价格计量，可参阅“定价详细信息”表下的[逻辑应用定价页](https://azure.microsoft.com/pricing/details/logic-apps/)。 |
|||

有关详细信息，请查看以下文档：

* [查看执行和存储使用情况的指标](plan-manage-costs.md#monitor-billing-metrics)
* [Azure 逻辑应用中的限制](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>未计量

* 由于不符合条件而跳过的触发器
* 由于逻辑应用在完成之前停止而未运行的操作
* [禁用的逻辑应用](#disabled-apps)

### <a name="other-related-resources"></a>其他相关资源

逻辑应用可用于其他相关资源，例如集成帐户、本地数据网关和集成服务环境 (ISE)。 若要了解这些资源的定价，请查看本主题后面的以下部分：

* [On-premises data gateway (本地数据网关)](#data-gateway)
* [集成帐户定价模型](#integration-accounts)
* [ISE 定价模型](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>消耗成本估计提示

以下提示有助于更准确地估计消耗成本：

* 请考虑任何给定日可能到达的消息或事件数，而不是仅基于轮询间隔进行计算。

* 当某个事件或消息满足触发器条件时，许多触发器将立即尝试读取任何其他满足条件的等待事件或消息。 此行为意味着，即使你选择较长的轮询间隔，触发器也基于符合启动工作流条件的等待事件或消息的数量进行触发。 遵循此行为的触发器包括 Azure 服务总线和 Azure 事件中心。

  例如，假设你设置了一个每天检查终结点的触发器。 当触发器检查终结点并找到 15 个满足条件的事件时，触发器触发并运行相应工作流 15 次。 逻辑应用服务会计量这 15 个工作流执行的所有操作，包括触发器请求。

<a name="standard-pricing"></a>

## <a name="standard-pricing-single-tenant"></a>标准定价（单租户）

基于托管计划和定价层的定价模型适用于在单租户 Azure 逻辑应用环境中运行的逻辑应用。 此定价适用于 Azure 门户中的逻辑应用（标准版）资源类型，或适用于使用 Visual Studio Code 的 Azure 逻辑应用（标准版）扩展来处理的逻辑应用。 创建或部署这类逻辑应用时，必须选择托管计划和定价层，以确定运行工作流时用于计量和计费的定价费率。

> [!NOTE]
> 对于使用逻辑应用（标准版）资源类型创建的新逻辑应用，必须使用“工作流标准”托管计划。 新的逻辑应用无法使用应用服务计划和应用服务环境。

<a name="hosting-plans"></a>

### <a name="pricing-tiers-and-billing-rates"></a>定价层和计费费率

托管计划中的每个定价层都包括特定数量的计算、内存和存储资源。 有关每个资源和每个区域的小时费率，请查看 [Azure 逻辑应用定价页](https://azure.microsoft.com/pricing/details/logic-apps/)。

为了更好地了解定价方式，本示例提供了美国东部 2 区域的示例估算值。

* 在 [Azure 逻辑应用定价页](https://azure.microsoft.com/pricing/details/logic-apps/)上，选择“美国东部 2”区域以查看小时费率，或查看下表：

  | 资源 | 每小时费用（美元）（美国东部 2） |
  |----------|------------------------|
  | 虚拟 CPU (vCPU) | 0\.192 美元 |
  | **内存** | 每 GB 0.0137 美元 |
  |||

* 根据上述信息，此表显示了每个定价层的预估每月费率以及该定价层中包含的资源：

  | 定价层 | 每月费用（美元）（美国东部 2） | 虚拟 CPU (vCPU) | 内存 (GB) | 存储器 (GB) |
  |--------------|-------------------------|--------------------|-------------|--------------|
  | **WS1** | 175.20 美元 | 1 | 3.5 | 250 |
  | **WS2** | 350.40 美元 | 2 | 7 | 250 |
  | **WS3** | 700.80 美元 | 4 | 14 | 250 |
  ||||||

* 根据上述信息，此表列出了每个资源，以及如果选择 WS1 定价层的预估每月费率：

  | 资源 | 金额 | 每月费用（美元）（美国东部 2） |
  |----------|--------|-------------------------|
  | 虚拟 CPU (vCPU) | 1 | 140.16 美元 |
  | **内存** | 3.5 GB | 35.04 美元 |
  ||||

<a name="storage-transactions"></a>

### <a name="storage-transactions"></a>存储事务

Azure 逻辑应用使用 [Azure 存储](../storage/index.yml)进行任何存储操作。 使用多租户 Azure 逻辑应用时，任何存储使用情况和成本都将附加到该逻辑应用。 使用单租户 Azure 逻辑应用时，可以使用自己的 Azure [存储帐户](../azure-functions/storage-considerations.md#storage-account-requirements)。 借助此功能，可以更灵活、更好地控制逻辑应用数据。

有状态工作流运行其操作时，Azure 逻辑应用运行时会执行存储事务。 例如，使用队列来进行计划，而使用表和 Blob 来存储工作流状态。 存储成本根据工作流的内容而变化。 不同的触发器、操作和有效负载会导致不同的存储操作和需求。 存储事务遵循 [Azure 存储定价模型](https://azure.microsoft.com/pricing/details/storage/)。 存储成本将在 Azure 账单发票中单独列出。

### <a name="tips-for-estimating-storage-needs-and-costs"></a>有关估算存储需求和成本的提示

为了帮助你了解工作流可能运行的存储操作的数量及其成本，请尝试使用[逻辑应用存储计算器](https://logicapps.azure.com/calculator)。 可以选择一个示例工作流或使用现有的工作流定义。 第一个计算估计工作流中的存储操作数。 然后，可以通过 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)使用这些数字来估算可能的成本。

有关详细信息，请参阅以下文档：

* [估算单租户 Azure 逻辑应用中工作流的存储需求和成本](estimate-storage-costs.md)
* [Azure 存储定价详细信息](https://azure.microsoft.com/pricing/details/storage/)

<a name="fixed-pricing"></a>

## <a name="ise-pricing-dedicated"></a>ISE 定价（专用）

固定的定价模型，适用于在专用[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中运行的逻辑应用。 ISE 按[集成服务环境价格](https://azure.microsoft.com/pricing/details/logic-apps)进行计费，具体取决于你创建的 [ISE 级别或 SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 此定价不同于多租户定价，因为无论是否使用保留容量和专用资源，都需要支付这些费用。

| ISE SKU | 说明 |
|---------|-------------|
| **高级** | 基本单元具有[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)，并且[对于高级 SKU，按每小时费率计费](https://azure.microsoft.com/pricing/details/logic-apps)。 如果需要更多的吞吐量，可以在创建 ISE 时或之后[添加更多缩放单元](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 每个缩放单元按[每小时费率计费，该费率大致为基本单元费率的一半](https://azure.microsoft.com/pricing/details/logic-apps)。 <p><p>有关容量和限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。 |
| **开发人员** | 基本单元具有[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)，并且[对于开发人员 SKU，按每小时费率计费](https://azure.microsoft.com/pricing/details/logic-apps)。 但是，此 SKU 在回收期间没有服务级别协议 (SLA)、纵向扩展功能或冗余，这意味着你可能会遇到延迟或停机。 后端更新可能会间歇性地中断服务。 <p><p>重要说明：确保将此 SKU 仅用于探索、试验、开发或测试，而不用于生产和性能测试。 <p><p>有关容量和限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。 |
|||

### <a name="included-at-no-extra-cost"></a>无需额外付费的项

| 项 | 描述 |
|-------|-------------|
| [内置](../connectors/built-in.md)的触发器和操作 | 显示“核心”标签，并在与逻辑应用程序相同的 ISE 中运行。 |
| [标准连接器](../connectors/managed.md) <p><p>[企业连接器](../connectors/managed.md#enterprise-connectors) | - 显示 ISE 标签的托管连接器，专门设计用于在没有本地数据网关的情况下运行，并在与逻辑应用相同的 ISE 中运行。 ISE 定价包含所需数量的企业连接。 <p><p>- 不显示 ISE 标签的连接器在单租户 Azure 逻辑应用服务中运行。 但是，ISE 定价包括在 ISE 中运行的逻辑应用的这些执行。 |
| [循环](logic-apps-control-flow-loops.md)内的操作 | ISE 定价涵盖了针对每个运行的循环周期在一个循环中运行的每个操作。 <p><p>例如，假设你有一个“for each”循环，其中包含列表处理操作。 若要获取操作执行的总数，请将列表项的数目乘以循环中的操作数，再加上启动循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。 |
| 重试次数 | 若要处理最基本的异常和错误，可以在支持的情况下为触发器和操作设置[重试策略](logic-apps-exception-handling.md#retry-policies)。 ISE 定价包括重试和原始请求。 |
| [数据保留和存储使用情况](#data-retention) | ISE 中的逻辑应用不会因数据保留和存储使用而产生费用。 |
| [集成帐户](#integration-accounts) | 包括用于单个集成帐户层的用量（基于 ISE SKU），无需额外付费。 |
|||

有关限制的信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[集成帐户](../logic-apps/logic-apps-pricing.md#integration-accounts)是你链接到逻辑应用的单独资源，以便可以浏览、构建和测试使用 [EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 处理](logic-apps-enterprise-integration-xml.md)功能的 B2B 集成解决方案。

根据你的逻辑应用是否在 Azure 逻辑应用（多租户或单租户）或 ISE 中运行，Azure 逻辑应用提供这些[定价](https://azure.microsoft.com/pricing/details/logic-apps/)和[计费模型各不相同](logic-apps-pricing.md#integration-accounts)的集成帐户级别或层级。

| 层 | 说明 |
|------|-------------|
| **基本** | 适用于只需处理消息或充当与大型企业实体有贸易合作关系的小型企业合作伙伴的情况。 <p><p>受逻辑应用 SLA 支持。 |
| **标准** | 适用于 B2B 关系更复杂且需要管理的实体数增加的情况。 <p><p>受逻辑应用 SLA 支持。 |
| **免费** | 适用于探索场景，不适用于生产场景。 此层对区域可用性、吞吐量和使用情况有限制。 例如，此免费层仅适用于 Azure 中的公共区域（如“美国西部”或“东南亚”），但不适用于 [Azure 中国世纪互联](/azure/china/overview-operations)或 [Azure 政府](../azure-government/documentation-government-welcome.md)。 <p><p>注意：不受逻辑应用 SLA 支持。 |
|||

有关集成帐户限制的详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)，例如：

* [对每个 Azure 订阅的集成帐户的限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [对每个集成帐户的各种项目的限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。 项目包含贸易合作伙伴、协议、映射、架构、程序集、证书、批处理配置等。

### <a name="multi-tenant-or-single-tenant-based-logic-apps"></a>基于多租户或单租户的逻辑应用

集成帐户按基于所用帐户层的固定[集成帐户价格](https://azure.microsoft.com/pricing/details/logic-apps/)进行计费。

### <a name="ise-based-logic-apps"></a>基于 ISE 的逻辑应用

ISE 包括单个集成帐户（基于 ISE SKU），无需额外付费。 可以支付额外的费用为 ISE 创建更多的集成帐户，最多可达到[ISE 总限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 详细了解本主题前面的 [ISE 定价模型](#fixed-pricing)。

| ISE SKU | 包含的集成帐户 | 额外费用 |
|---------|------------------------------|-----------------|
| **高级** | 单个[标准](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户 | 最多 19 个标准帐户。 不允许使用免费帐户或基本帐户。 |
| **开发人员** | 单个[免费](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户 | 如果你已有一个免费帐户，则最多可有 19 个标准帐户，如果你没有免费帐户，则最多可有 20 个标准帐户。 不允许使用基本帐户。 |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-usage"></a>数据保留和存储使用情况

Azure 逻辑应用使用 [Azure 存储](../storage/index.yml)进行任何存储操作。 工作流运行历史记录的输入和输出根据逻辑应用的[运行历史记录保留限制](logic-apps-limits-and-config.md#run-duration-retention-limits)来存储和计量。 要监视存储使用情况，请参阅[查看执行和存储使用情况的指标](plan-manage-costs.md#monitor-billing-metrics)。

| 环境 | 说明 |
|-------------|-------|
| **多租户** | 存储使用情况和数据保留使用固定费率计费，可以在[逻辑应用定价页](https://azure.microsoft.com/pricing/details/logic-apps)的“定价详细信息”表下找到该费率。 |
| **单租户** | 存储使用情况和数据保留使用 [Azure 存储定价模型](https://azure.microsoft.com/pricing/details/storage/)计费。 存储成本将在 Azure 账单发票中单独列出。 有关详细信息，请查看[存储交易（单租户）](#storage-transactions)。 |
| **ISE** | 存储使用和数据保留不会产生费用。 |
|||

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>本地数据网关

[本地数据网关](../logic-apps/logic-apps-gateway-install.md)是你创建的单独资源，以便逻辑应用工作流可以使用特定网关支持的连接器访问本地数据。 通过网关运行的连接器操作会产生费用，但网关本身不会产生费用。

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps-or-workflows"></a>禁用的逻辑应用或工作流

禁用的逻辑应用（多租户）或工作流（单租户）不会产生费用，因为它们在禁用时无法创建新实例。

## <a name="next-steps"></a>后续步骤

* [计划和管理 Azure 逻辑应用的成本](plan-manage-costs.md)