---
title: 计费 & 定价模型
description: 有关定价和计费模型在 Azure 逻辑应用中的工作原理的概述
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 2b37308bcbcd489876c21dce56878de7e0daf545
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699022"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>适用于 Azure 逻辑应用的定价和计费模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍如何使用逻辑应用服务和相关资源的计费和定价模型。 有关具体的定价费率，请参阅 [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。 若要了解如何计划、管理和监视成本，请参阅 [计划和管理 Azure 逻辑应用的成本](plan-manage-costs.md)。

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>多租户定价

使用的付费消耗定价模型适用于在公共、"全局"、多租户逻辑应用服务中运行的逻辑应用。 所有成功和失败的运行都按流量计费并计费。

例如，轮询触发器所进行的请求仍会按流量计量，即使该触发器已被跳过，也不会创建逻辑应用工作流实例。

| 项 | 描述 |
|-------|-------------|
| [内置](../connectors/apis-list.md#built-in) 触发器和操作 | 在逻辑应用服务中以本机方式运行，并使用 [**操作** 价格](https://azure.microsoft.com/pricing/details/logic-apps/)按流量计费。 <p><p>例如，HTTP 触发器和请求触发器是内置触发器，而 HTTP 操作和响应操作是内置操作。 数据操作、批处理操作、变量操作和 [工作流控制操作](../connectors/apis-list.md#control-workflow)（如循环、条件、开关、并行分支等）也是内置操作。 |
| [标准连接器](../connectors/apis-list.md#managed-connectors) 触发器和操作 <p><p>[自定义连接器](../connectors/apis-list.md#custom) 触发器和操作 | 使用 [标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps/)按流量计费。 |
| [企业连接器](../connectors/apis-list.md#managed-connectors) 触发器和操作 | 使用 [企业连接器价格](https://azure.microsoft.com/pricing/details/logic-apps/)按流量计费。 但是，在公共预览版期间，企业连接器使用 [*标准* 连接器价格](https://azure.microsoft.com/pricing/details/logic-apps/)进行计费。 |
| [循环](logic-apps-control-flow-loops.md)内的操作 | 循环中运行的每个操作都按运行的每个循环周期计费。 <p><p>例如，假设您有一个 "for each" 循环，其中包含处理列表的操作。 逻辑应用服务计量在该循环中运行的每个操作，方法是将列表项的数目乘以循环中的操作数，然后添加开始循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。 |
| 重试次数 | 若要处理最基本的异常和错误，可以在支持的情况下为触发器和操作设置 [重试策略](logic-apps-exception-handling.md#retry-policies) 。 根据触发器或操作具有内置、标准还是企业类型，这些重试与原始请求一起收费。 例如，使用2次重试执行的操作将按3次操作执行收费。 |
| [数据保留和存储消耗](#data-retention) | 使用数据保留价格按流量计费，可在 "**定价详细信息**" 表下的 "[逻辑应用定价" 页](https://azure.microsoft.com/pricing/details/logic-apps/)中找到。 |
|||

有关详细信息，请参阅以下主题：

* [查看执行和存储消耗的指标](plan-manage-costs.md#monitor-billing-metrics)
* [Azure 逻辑应用中的限制](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>未计量

* 由于不符合条件而跳过的触发器
* 由于逻辑应用在完成之前停止而未运行的操作
* [禁用的逻辑应用](#disabled-apps)

### <a name="other-related-resources"></a>其他相关资源

逻辑应用可用于其他相关资源，例如集成帐户、本地数据网关和 integration service 环境 (ISEs) 。 若要了解这些资源的定价，请查看本主题后面的以下部分：

* [On-premises data gateway (本地数据网关)](#data-gateway)
* [集成帐户定价模型](#integration-accounts)
* [ISE 定价模型](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>估计消耗成本的提示

若要帮助您估算更准确的消耗成本，请查看以下提示：

* 考虑可能会在任意给定日期到达的消息或事件的数目，而不是仅基于轮询间隔来计算结果。

* 当某个事件或消息满足触发器条件时，许多触发器将立即尝试读取满足条件的任何和所有其他等待事件或消息。 此行为意味着，即使你选择较长的轮询间隔，触发器也基于符合启动工作流条件的等待事件或消息的数量进行触发。 遵循此行为的触发器包括 Azure 服务总线和 Azure 事件中心。

  例如，假设设置了每天检查终结点的触发器。 当触发器检查终结点并找到 15 个满足条件的事件时，触发器触发并运行相应工作流 15 次。 逻辑应用服务计量15个工作流执行的所有操作，包括触发器请求。

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE 定价

固定定价模型适用于在 [ (ISE) 的 *integration service 环境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行的逻辑应用。 ISE 使用 [集成服务环境价格](https://azure.microsoft.com/pricing/details/logic-apps)计费，具体取决于你创建的 [ISE 级别或 *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 。 此定价不同于多租户定价，因为无论是否使用保留容量和专用资源，都需要支付这些费用。

| ISE SKU | 说明 |
|---------|-------------|
| **高级** | 基本单位具有 [固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise) ，并 [按每小时费率收费，适用于高级 SKU](https://azure.microsoft.com/pricing/details/logic-apps)。 如果需要更多的吞吐量，则可以在创建 ISE 或之后 [添加更多缩放单位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 。 每个缩放单位按 [小时费率计费，该费率大致为基本单位费率的一半](https://azure.microsoft.com/pricing/details/logic-apps)。 <p><p>有关容量和限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。 |
| **开发人员** | 基本单位具有 [固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise) ，并 [按开发人员 SKU 的每小时费率计费](https://azure.microsoft.com/pricing/details/logic-apps)。 但是，在回收期间，此 SKU 没有服务级别协议 (SLA) 、扩展功能或冗余，这意味着你可能会遇到延迟或停机时间。 后端更新可能会中断服务。 <p><p>**重要说明**：请确保将此 SKU 仅用于勘探、试验、开发和测试，而不能用于生产或性能测试。 <p><p>有关容量和限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。 |
|||

### <a name="included-at-no-extra-cost"></a>无需额外付费

| 项 | 描述 |
|-------|-------------|
| [内置](../connectors/apis-list.md#built-in) 触发器和操作 | 显示 " **核心** " 标签，并在与逻辑应用相同的 ISE 中运行。 |
| [标准连接器](../connectors/apis-list.md#managed-connectors) <p><p>[企业连接器](../connectors/apis-list.md#enterprise-connectors) | -显示 **ISE** 标签的托管连接器专门设计用于在没有本地数据网关的情况下运行，并在与逻辑应用相同的 ISE 中运行。 ISE 定价包含所需数量的企业连接。 <p><p>-不显示 ISE 标签的连接器在多租户逻辑应用服务中运行。 但是，ISE 定价包括在 ISE 中运行的逻辑应用的这些执行。 |
| [循环](logic-apps-control-flow-loops.md)内的操作 | ISE 定价包括为运行的每个循环周期在循环中运行的每个操作。 <p><p>例如，假设您有一个 "for each" 循环，其中包含处理列表的操作。 若要获取操作执行的总数，请将列表项的数目乘以循环中的操作数，并添加开始循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。 |
| 重试次数 | 若要处理最基本的异常和错误，可以在支持的情况下为触发器和操作设置 [重试策略](logic-apps-exception-handling.md#retry-policies) 。 ISE 定价包括重试和原始请求。 |
| [数据保留和存储消耗](#data-retention) | ISE 中的逻辑应用不会产生保留和存储成本。 |
| [集成帐户](#integration-accounts) | 包括基于 ISE SKU 的单个集成帐户层的使用情况，无需额外付费。 |
|||

有关限制信息，请参阅 [Azure 逻辑应用中的 ISE 限制](logic-apps-limits-and-config.md#integration-service-environment-ise)。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[集成帐户](../logic-apps/logic-apps-pricing.md#integration-accounts)是您创建并链接到逻辑应用的单独资源，以便您可以浏览、构建和测试使用[EDI](logic-apps-enterprise-integration-b2b.md)和[XML 处理](logic-apps-enterprise-integration-xml.md)功能的 B2B 集成解决方案。

根据逻辑应用是基于消耗还是基于 ISE 的，Azure 逻辑应用提供了 [不同定价](https://azure.microsoft.com/pricing/details/logic-apps/) 和 [计费模型](logic-apps-pricing.md#integration-accounts)的各种集成帐户级别或层：

| 层 | 说明 |
|------|-------------|
| **基本** | 适用于你只希望进行消息处理或充当与较大业务实体具有贸易合作伙伴关系的小型企业合作伙伴的情况。 <p><p>受逻辑应用 SLA 支持。 |
| **标准** | 适用于你具有更复杂 B2B 关系并增加了你必须管理的实体数的方案。 <p><p>受逻辑应用 SLA 支持。 |
| **免费** | 适用于探索方案，而不是生产方案。 此层对区域可用性、吞吐量和使用量有限制。 例如，免费级别仅适用于 Azure 中的公共区域，例如 "美国西部" 或 "东南亚"，但不适用于 [Azure 中国世纪互联](/azure/china/overview-operations) 或 [azure 政府](../azure-government/documentation-government-welcome.md)版。 <p><p>**注意**：逻辑应用 SLA 不支持。 |
|||

有关集成帐户限制的详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)，例如：

* [每个 Azure 订阅的集成帐户限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [每个集成帐户的各种项目限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。 项目包括贸易合作伙伴、协议、地图、架构、程序集、证书、批配置等。

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>基于消耗的逻辑应用的集成帐户

集成帐户使用基于你使用的帐户层的固定 [集成帐户价格](https://azure.microsoft.com/pricing/details/logic-apps/) 进行计费。

### <a name="ise-based-logic-apps"></a>基于 ISE 的逻辑应用

ISE 无需额外付费，基于 ISE SKU 提供单个集成帐户。 为实现额外的成本，你可以为 ISE 创建更多的集成帐户，使其最多使用 [总 ISE 限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 详细了解本主题前面的 [ISE 定价模型](#fixed-pricing) 。

| ISE SKU | 包含的集成帐户 | 额外成本 |
|---------|------------------------------|-----------------|
| **高级** | 单个 [标准](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 集成帐户 | 多达19个标准帐户。 不允许使用免费帐户或基本帐户。 |
| **开发人员** | 单个 [免费](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 集成帐户 | 如果你已有一个免费帐户，则多达19个标准帐户，如果你没有免费帐户，则最多可以有20个标准帐户。 不允许使用基本帐户。 |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>数据保留和存储消耗

逻辑应用的运行历史记录中的所有输入和输出都是基于该应用程序的 [运行持续时间和历史记录保持期](logic-apps-limits-and-config.md#run-duration-retention-limits)进行存储和计量的。

* 对于多租户逻辑应用服务中的逻辑应用，将按固定价格对存储消耗计费，这可在 "**定价详细信息**" 表下的 "[逻辑应用定价" 页](https://azure.microsoft.com/pricing/details/logic-apps)中找到。

* 对于 ISEs 中的逻辑应用，存储消耗不会产生数据保留成本。

若要监视存储使用情况，请参阅 [查看执行和存储消耗的指标](plan-manage-costs.md#monitor-billing-metrics)。

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>本地数据网关

[本地数据网关](../logic-apps/logic-apps-gateway-install.md)是你创建的单独资源，以便逻辑应用可以使用特定于网关支持的连接器访问本地数据。 通过网关运行的连接器操作会产生费用，但网关本身不会产生费用。

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>禁用的逻辑应用

禁用的逻辑应用在禁用期间不会产生费用，因为它们无法创建新实例。 禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

## <a name="next-steps"></a>后续步骤

* [规划和管理 Azure 逻辑应用的成本](plan-manage-costs.md)
