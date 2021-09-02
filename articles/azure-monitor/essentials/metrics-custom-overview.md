---
title: Azure Monitor 中的自定义指标（预览版）
description: 了解 Azure Monitor 中的自定义指标及其建模方式。
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a456db938d1607565525aea7ab2e9b43bc368aeb
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195198"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Monitor 中的自定义指标（预览版）

在 Azure 中部署资源和应用程序时，需要开始收集遥测数据，以洞察它们的性能和运行状况。 Azure 提供一些现成的指标。 这些指标称为[标准指标或平台指标](./metrics-supported.md)。 但是，它们在性质上有限制。 

可能需要收集一些自定义性能指标或特定于业务的指标才能提供更深入的见解。 可以通过应用程序遥测、Azure 资源上运行的代理甚至从外到内的监视系统收集这些 **自定义** 指标，然后将其直接提交给 Azure Monitor。 发布到 Azure Monitor 之后，可以连同 Azure 发出的标准指标一起浏览、查询 Azure 资源和应用程序的自定义指标，并针对其发出警报。

Azure Monitor 自定义指标目前为公开预览版。 

## <a name="methods-to-send-custom-metrics"></a>用于发送自定义指标的方法

可以通过多种方法将自定义指标发送到 Azure Monitor：
- 使用 Azure Application Insights SDK 检测应用程序并将自定义遥测数据发送到 Azure Monitor。 
- 在 [Windows 或 Linux Azure VM](../agents/azure-monitor-agent-overview.md) 上安装 Azure Monitor 代理（预览版），并使用[数据收集规则](../agents/data-collection-rule-azure-monitor-agent.md)将性能计数器发送到 Azure Monitor 指标。
- 在 [Azure VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)、[虚拟机规模集](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)、[经典 VM](../essentials/collect-custom-metrics-guestos-vm-classic.md) 或[经典云服务](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)上安装 Windows Azure 诊断 (WAD) 扩展，并将性能计数器发送到 Azure Monitor。 
- 在 Azure Linux VM 上安装 [InfluxData Telegraf 代理](../essentials/collect-custom-metrics-linux-telegraf.md)，并使用 Azure Monitor 输出插件发送指标。
- 将自定义指标[直接发送到 Azure Monitor REST API](./metrics-store-custom-rest-api.md)：`https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`。

## <a name="pricing-model-and-retention"></a>定价模型和保留期

查看 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)，了解何时为自定义指标和指标查询启用计费的详细信息。 本页提供所有指标的特定价格详细信息，包括自定义指标和指标查询。 总之，将标准指标（平台指标）引入 Azure Monitor 指标存储不会产生费用，但是自定义指标在正式发布后会产生费用。 指标 API 查询会产生费用。

自定义指标的保留时间[与平台指标的保留时间相同](../essentials/data-platform-metrics.md#retention-of-metrics)。 

> [!NOTE]  
> 通过 Application Insights SDK 发送到 Azure Monitor 的指标将按引入的日志数据计费。 仅当选择了 Application Insights 功能[在自定义指标维度上启用警报](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)时，它们才会产生额外的指标费用。 此复选框使用自定义指标 API 将数据发送到 Azure Monitor 指标数据库，以允许更复杂的警报。  详细了解 [Application Insights 定价模型](../app/pricing.md#pricing-model)和[你所在区域的定价](https://azure.microsoft.com/pricing/details/monitor/)。


## <a name="how-to-send-custom-metrics"></a>如何发送自定义指标

将自定义指标发送到 Azure Monitor 时，报告的每个数据点或值必须包括以下信息。

### <a name="authentication"></a>身份验证
若要将自定义指标提交到 Azure Monitor，提交指标的实体需在请求的 **Bearer** 标头中提供有效的 Azure Active Directory (Azure AD) 令牌。 可通过几种支持的方法获取有效的持有者令牌：
1. [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md) 为 Azure 资源本身（例如 VM）提供一个标识。 托管服务标识 (MSI) 旨在授予资源权限来执行特定的操作。 例如，允许资源发出有关其自身的指标。 可为某个资源或其 MSI 授予针对另一个资源的“监视指标发布者”权限。 获取此权限后，该 MSI 也能发出其他资源的指标。
2. [Azure AD 服务主体](../../active-directory/develop/app-objects-and-service-principals.md)。 在此方案中，可向某个 Azure AD 应用程序或服务分配发出有关 Azure 资源的指标的权限。
为了对请求进行身份验证，Azure Monitor 将使用 Azure AD 公钥来验证应用程序令牌。 现有的“监视指标发布者”角色已拥有此权限。 可在 Azure 门户中使用此权限。 可以根据服务主体要发出哪些资源的自定义指标，在所需的范围为该服务主体授予“监视指标发布者”角色。 范围的示例包括订阅、资源组或特定资源。

> [!TIP]  
> 请求用于发出自定义指标的 Azure AD 令牌时，请确保请求该令牌的受众或资源是 `https://monitoring.azure.com/`。 请务必包含尾部的“/”。

### <a name="subject"></a>使用者
此属性捕获自定义指标报告的 Azure 资源 ID。 将在 API 调用的 URL 中为此信息编码。 每个 API 只能提交单个 Azure 资源的指标值。

> [!NOTE]  
> 无法针对资源组或订阅的资源 ID 发出自定义指标。


### <a name="region"></a>区域
此属性捕获针对其发出指标的资源所部署到的 Azure 区域。 必须将指标发出到资源部署区域所在的同一个 Azure Monitor 区域终结点。 例如，部署在美国西部的 VM 的自定义指标必须发送到美国西部区域 Azure Monitor 终结点。 区域信息也在 API 调用的 URL 中编码。

> [!NOTE]  
> 在公共预览期，自定义指标只在一部分 Azure 区域中可用。 本文稍后的部分提供了受支持区域的列表。
>
>

### <a name="timestamp"></a>Timestamp
发送到 Azure Monitor 的每个数据点必须带有时间戳标记。 此时间戳捕获测量或收集指标值的日期时间。 Azure Monitor 接受带有过去最多 20 分钟和将来最多 5 分钟时间戳的指标数据。 时间戳必须采用 ISO 8601 格式。

### <a name="namespace"></a>命名空间
命名空间可将类似的指标分类或分组到一起。 使用命名空间能够在可收集不同见解或性能指标的指标组之间实现隔离。 例如，可以使用名为 **contosomemorymetrics** 的命名空间来跟踪用于分析应用的内存用量指标， 并使用名为 **contosoapptransaction** 的另一个命名空间来跟踪有关应用程序中用户事务的所有指标。

### <a name="name"></a>名称
“名称”是正在报告的指标的名称。 通常，该名称具有足够的自述性，可帮助识别所要测量的指标。 以测量给定 VM 上所用内存字节数的指标为例， 该指标可以使用类似于“已使用内存字节数”的名称。

### <a name="dimension-keys"></a>维度键
维度是一个键/值对，帮助描述有关收集的指标的附加特征。 使用附加特征可以收集用于提供更深入见解的指标的详细信息。 例如，“已使用内存字节数”指标可以包含名为“进程”的维度键，用于捕获 VM 上的每个进程消耗的内存字节数。  使用此键可以筛选指标，以查看特定的进程使用了多少内存，或者识别内存用量最高的 5 个进程。
维度是可选的，并非所有指标都可能包含维度。 一个自定义指标最多可以包含 10 个维度。

### <a name="dimension-values"></a>维度值
报告指标数据点时，所报告的指标的每个维度键都有一个对应的维度值。 例如，可以报告 VM 上 ContosoApp 使用的内存：

* 指标名称是“已使用内存字节数”。
* 维度键是“进程”。
* 维度值是“ContosoApp.exe”。

发布指标值时，只能为每个维度键指定一个维度值。 如果收集 VM 上多个进程的相同“内存使用率”指标，则可以报告该时间戳的多个指标值。 每个指标值将为“进程”维度键指定不同的维度值。
维度是可选的，并非所有指标都可能包含维度。 如果指标帖子定义维度键，则相应的维度值是必需的。

### <a name="metric-values"></a>指标值
Azure Monitor 以一分钟粒度间隔存储所有指标。 我们知道，在给定的分钟内，某个指标可能需要多次采样， 例如 CPU 利用率。 或者，可能需要针对多个离散事件进行测量， 例如登录事务延迟。 若要在 Azure Monitor 中限制发出和支付的原始值数目，可在本地预先聚合并发出值：

* **最小值**：在给定的分钟内从所有样本和测量值中观测到的最小值。
* **最大值**：在给定的分钟内从所有样本和测量值中观测到的最大值。
* **总和**：在给定的分钟内从所有样本和测量值中观测到的所有值的总和。
* **计数**：在给定的分钟内创建的样本和测量值数目。

例如，如果在给定的分钟内，应用中出现 4 个登录事务，则为每个事务测量到的延迟可能如下所示：

|事务 1|事务 2|事务 3|事务 4|
|---|---|---|---|
|7 毫秒|4 毫秒|13 毫秒|16 毫秒|

发布到 Azure Monitor 的最终指标如下：
* 最小值：4
* 最大值：16
* 总和：40
* 计数：4

如果应用程序无法在本地预先聚合，并需要在收集后立即发出每个离散样本或事件，则你可以发出原始测量值。 例如，每当应用中出现登录事务时，就可以向 Azure Monitor 发布包含单个测量值的指标。 因此，对于花费了 12 毫秒的登录事务，指标发布内容如下：
* 最小值：12
* 最大值：12
* 总和：12
* 计数：1

使用此过程可以发出在给定的分钟内，相同指标与维度的组合的多个值。 然后，Azure Monitor 将会提取在给定分钟内发出的所有原始值，并将其聚合在一起。

### <a name="sample-custom-metric-publication"></a>样本自定义指标发布
以下示例在虚拟机的“内存分析”指标命名空间下创建名为“已使用内存字节数”的自定义指标。  该指标包含名为“进程”的单个维度。 对于给定的时间戳，我们将发出两个不同进程的指标值：

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights、诊断扩展和 InfluxData Telegraf 代理已配置为针对正确的区域终结点发出指标值，每次发出都会携带上述所有属性。
>
>

## <a name="custom-metric-definitions"></a>自定义指标的定义
在发出自定义指标之前，无需在 Azure Monitor 中预定义该指标。 发布的每个指标数据点包含命名空间、名称和维度信息。 因此，首次将自定义指标发送到 Azure Monitor 时，会自动创建指标定义。 然后，可在通过指标定义发出其指标的任何资源上发现此指标定义。

> [!NOTE]  
> Azure Monitor 尚不支持为自定义指标定义“单位”。

## <a name="using-custom-metrics"></a>使用自定义指标
将自定义指标提交到 Azure Monitor 之后，可以通过 Azure 门户浏览它们，以及通过 Azure Monitor REST API 查询它们。 还可以对其创建警报，以便在满足特定的条件时收到通知。

> [!NOTE]
> 你需要具有“读者”或“参与者”角色才能查看自定义指标。 请参阅[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader)。 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>通过 Azure 门户浏览自定义指标
1.    转到 [Azure 门户](https://portal.azure.com)。
2.    选择“监视”窗格。
3.    选择“指标”。
4.    选择已针对其发出自定义指标的资源。
5.    选择自定义指标的指标命名空间。
6.    选择自定义指标。

> [!NOTE]
> 有关在 Azure 门户中查看指标的详细信息，请参阅 [Azure 指标资源管理器入门](./metrics-getting-started.md)。

## <a name="supported-regions"></a>支持的区域
在公共预览期，发布自定义指标的功能只在一部分 Azure 区域中可用。 此限制意味着，只能发布某个受支持区域中的资源的指标。 有关 Azure 区域的详细信息，请参阅 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 以下终结点中使用的 Azure 区域代码只是去除了空格的区域名称。下表列出了自定义指标支持的 Azure 区域集。 另外还列出了这些区域中的资源的指标应发布到的相应终结点：

|Azure 区域 |区域终结点前缀|
|---|---|
| 所有公有云区域 | https://<azure_region_code>.monitoring.azure.com |


## <a name="latency-and-storage-retention"></a>延迟和存储保留期

添加全新指标或向指标添加新维度后，可能需要 2 到 3 分钟才能显示。 在系统中，99% 的情况下数据应在 30 秒内显示。 

如果删除指标或维度，则从系统中删除更改可能需要一周到一个月的时间。

## <a name="quotas-and-limits"></a>配额和限制
Azure Monitor 针对自定义指标实施以下用量限制：

|Category|限制|
|---|---|
|活动的时序/订阅/区域|50,000|
|每个指标的维度键数|10|
|指标命名空间、指标名称、维度键和维度值的字符串长度|256 个字符|

活动的时序定义为包含过去 12 小时内发布的指标值的指标、维度键或维度值的任意唯一组合。

若要了解 50k 时序限制，请考虑以下指标：

含 Region、Department 和 CustomerID 维度的服务器响应时间   

对于这一指标，如果有 10 个区域、20 个部门和 100 个客户，则共有 10 x 20 x 100 = 2000 个时序。 

如果有 100 个区域、200 个部门和 2000 个客户，则共有 100 x 200 x 2000 = 40000000 个时序，仅此指标就远远超出限制。 

同样，这并非单个指标的限制， 而是整个订阅和区域中所有此类指标的总和限制。  

## <a name="design-limitations-and-considerations"></a>设计限制和注意事项

请勿使用 Application Insights 进行审核 – Application Insights 遥测管道已进行优化，将性能影响降至最低，并限制监视应用程序所产生的网络流量。 因此，当初始数据集变得过大时，就会进行限制或采样（仅获取一定百分比的遥测数据，而忽略其余数据）。 由于这种行为，您不能将其用于审核目的，因为某些记录可能会被删除。 

名称中包含变量的指标 – 不要在指标名称中使用变量，请改用常量。 每次变量更改其值时，Azure Monitor 都会生成新的指标，导致很快达到指标数限制。 通常，当开发人员希望在指标名称中包括变量时，他们实际是希望在一个指标内跟踪多个时序，应使用维度代替变量指标名称。 

高基数指标维度 - 在某一维度中包含太多有效值（称为“高基数”）的指标更有可能达到 50k 的限制。 通常，不应在维度中使用不断改变的值。 例如，不应将时间戳用作维度。 可以使用服务器、客户或产品 ID，但前提是其中每一种类型的数量都较少。 作为测试，问问你自己，你是否会在图形上绘制此类数据。  你有 10 台甚至 100 台服务器时，将它们全部显示在图形上进行比较可能会很有帮助。 但是，您有 1000 台服务器时，生成的图形即使不是无法阅读，也会难以阅读。 最佳做法是将有效值数量保持在 100 之下， 从 100 到 300 是灰色区域。  如果需要超出此数量，请改用 Azure Monitor 自定义日志。   

当名称中包含变量，或者使用高基数维度时，可能会发生以下情况：
- 由于进行限制，指标变得不可靠
- 指标资源管理器无法正常工作
- 警报和通知变得不可预测
- 成本可能会意外增加 - 当包含维度的自定义指标为公共预览版时，Microsoft 不收费。 但是，一旦将来开始收费，就会产生意外的费用。 该套餐会根据所监视的时序数以及所执行的 API 调用数，对指标消耗进行收费。

## <a name="next-steps"></a>后续步骤
在不同的服务中使用自定义指标： 
 - [虚拟机](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [虚拟机规模集](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure 虚拟机（经典）](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [使用 Telegraf 代理的 Linux 虚拟机](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [经典云服务](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
