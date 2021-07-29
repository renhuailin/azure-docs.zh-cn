---
title: 使用 Key Vault 见解监视 Key Vault | Microsoft Docs
description: 本文介绍 Key Vault 见解。
services: azure-monitor
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: 953d6300b8ac19ddfcfe3b37a1cfde50480e19a4
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110095115"
---
# <a name="monitoring-your-key-vault-service-with-key-vault-insights"></a>使用 Key Vault 见解监视密钥保管库服务
Key Vault 见解提供 Key Vault 请求、性能、失败和延迟的统一视图，在其中可以全面监视密钥保管库。
本文将帮助你了解如何加入和自定义 Key Vault 见解的体验。

## <a name="introduction-to-key-vault-insights"></a>Key Vault 见解简介

在深入了解此体验之前，应该了解它如何呈现和可视化信息。
-    **大规模透视图**：可显示基于请求的性能快照视图、故障详情以及操作和延迟概览。
-   **深化分析**：可对特定密钥保管库执行详细分析。
-    **可自定义**：可在其中更改要查看、修改的指标，或设置与限制相符的阈值，并保存你自己的工作簿。 可以将工作簿中的图表固定到 Azure 面板。

Key Vault 见解结合了日志和指标，可提供全局监视解决方案。 所有用户都可以访问基于指标的监视数据，但包含基于日志的可视化效果可能要求用户[对其 Azure 密钥保管库启用日志记录](../../key-vault/general/logging.md)。

## <a name="view-from-azure-monitor"></a>从 Azure Monitor 查看

在 Azure Monitor 中，可以查看订阅中多个密钥保管库的请求、延迟和故障详细信息，并帮助确定性能问题和限制方案。

若要查看所有订阅中密钥保管库的利用率和操作情况，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 从 Azure 门户的左侧窗格中选择“监视”，然后在“见解”部分下选择“密钥保管库” 。

![“概览”体验的屏幕截图，其中显示了多个关系图](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>“概览”工作簿

在所选订阅的“概览”工作簿中，表显示了分组到该订阅的密钥保管库的交互式密钥保管库指标。 你可以根据从下面的下拉列表中选择的选项来筛选结果：

* 订阅 – 只列出包含密钥保管库的订阅。

* Key Vault – 默认情况下，最多只会预先选择 5 个密钥保管库。 如果在作用域选择器中选择全部或多个密钥保管库，则将返回最多 200 个密钥保管库。 例如，如果在你选择的三个订阅中共有 573 个密钥保管库，则将仅显示 200 个保管库。

* 时间范围 – 默认情况下，系统会根据你所做的选择显示过去 24 小时的相应信息。

下拉列表下的计数器磁贴汇总了所选订阅中密钥保管库的总数并反映了选择的数目。 报告请求、故障和延迟指标的工作簿的列存在条件颜色编码的热度地图。 最深的颜色具有最高值，较浅的颜色基于最低值。

## <a name="failures-workbook"></a>“故障”工作簿

在页面顶部选择“故障”，“故障”选项卡随即打开。 其中显示了 API 命中率、一段时间的频率以及某些响应代码的数量。

![“故障”工作簿的屏幕截图](./media/key-vaults-insights-overview/failures.png)

工作簿中报告蓝色值的 API 命中数指标的列存在条件颜色编码或热度地图。 最深的颜色具有最高值，较浅的颜色基于最低值。

工作簿显示成功（2xx 状态代码）、身份验证错误（401/403 状态代码）、限制（429 状态代码）和其他故障（4xx 状态代码）。

为了更好地理解每个状态代码的含义，建议通读有关 [Azure 密钥保管库状态和响应代码](../../key-vault/general/authentication-requests-and-responses.md)的文档。

## <a name="view-from-a-key-vault-resource"></a>从密钥保管库资源查看

若要直接从密钥保管库访问 Key Vault 见解，请执行以下操作：

1. 在 Azure 门户中，选择密钥保管库。

2. 从列表中选择一个密钥保管库。 在“监视”部分中，选择“见解”。

还可以从 Azure Monitor 级别工作簿中选择密钥保管库的资源名称来访问这些视图。

![密钥保管库资源视图的屏幕截图](./media/key-vaults-insights-overview/key-vault-resource-view.png)

在密钥保管库的“概览”工作簿中，显示了一些可帮助你快速评估的性能指标：

- 交互式性能图表，显示与密钥保管库事务、延迟和可用性相关的最重要的详细信息。

- 指标和状态磁贴，突出显示服务可用性、密钥保管库资源的事务总数和总体延迟。

选择“故障”或“操作”的任何其他选项卡会打开相应的工作簿 。

![“故障”视图的屏幕截图](./media/key-vaults-insights-overview/resource-failures.png)

“故障”工作簿将细分所选期限内全部密钥保管库请求的结果，并提供成功 (2xx)、身份验证错误 (401/403)、限制 (429) 和其他故障几个分类。

![“操作”视图的屏幕截图](./media/key-vaults-insights-overview/operations.png)

借助“操作”工作簿，用户可深入了解所有事务的完整详细信息，可以使用顶层磁贴按“结果状态”筛选这些事务。

![此屏幕截图显示了包含所有事务的完整详细信息的“操作”工作簿。](./media/key-vaults-insights-overview/info.png)

用户还可以根据上表中的特定事务类型来确定视图的作用域，这会动态更新下表，用户可以在弹出的上下文窗格中查看完整的操作详细信息。

>[!NOTE]
> 请注意，用户必须启用了诊断设置才能查看此工作簿。 要详细了解如何启用诊断设置，请阅读有关 [Azure 密钥保管库日志记录](../../key-vault/general/logging.md)的详细信息。

## <a name="pin-and-export"></a>固定和导出

可将任一指标部分固定到 Azure 仪表板，方法是选择该部分右上角的图钉图标。

在多订阅和密钥保管库“概览”或“失败”工作簿中，支持通过选择图钉图标左侧的下载图标来导出 Excel 格式的结果。

![已选择的固定图标的屏幕截图](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-key-vault-insights"></a>自定义 Key Vault 见解

本部分重点介绍编辑工作簿以自定义支持数据分析需求的常见方案：
*  将工作簿的作用域限定为始终选择特定订阅或密钥保管库
* 更改网格中的指标
* 更改请求阈值
* 更改颜色渲染

从顶部工具栏选择“自定义”按钮可启用编辑模式，以开始自定义。

![“自定义”按钮的屏幕截图](./media/key-vaults-insights-overview/customize.png)

自定义项保存到自定义工作簿，以防止覆盖已发布工作簿中的默认配置。 无论是在专用的“我的报表”部分，还是在有权访问资源组的任何用户均可访问的“共享报表”部分，工作簿都保存在某个资源组中。 保存自定义工作簿后，需要转到工作簿库来启动它。

![工作簿库的屏幕截图](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>指定订阅或密钥保管库

你可以将包含多个订阅和密钥保管库的“概览”或“故障”工作簿配置为在每次运行时作用于特定的订阅或密钥保管库，具体操作步骤如下：

1. 从门户中选择“监视”，然后从左侧窗格中选择“密钥保管库” 。
2. 在“概览”工作簿上，从命令栏中选择“编辑” 。
3. 从“订阅”下拉列表中选择要用作默认订阅的一个或多个订阅。 请记住，工作簿最多支持选择 10 个订阅。
4. 从“密钥保管库”下拉列表中选择要用作默认帐户的一个或多个帐户。 请记住，工作簿最多支持选择 200 个存储帐户。
5. 选择命令栏中的“另存为”，以保存包含自定义项的工作簿副本，然后单击“完成编辑”返回到阅读模式。

## <a name="troubleshooting"></a>故障排除

如需常规故障排除指南，请参阅专用的基于工作簿的见解[故障排除文章](troubleshoot-workbooks.md)。

本部分将帮助你诊断和排查在使用 Key Vault 见解时可能会遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="resolving-performance-issues-or-failures"></a>解决性能问题或故障

若要排查使用 Key Vault 见解确定的任何密钥保管库相关问题，请参阅 [Azure Key Vault 文档](../../key-vault/index.yml)。

### <a name="why-can-i-only-see-200-key-vaults"></a>为什么只能看到 200 个密钥保管库

最多只能选择和查看 200 个密钥保管库。 无论所选订阅数量是多少，所选密钥保管库的数目限制为 200。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>为什么在订阅选取器中看不到所有订阅

我们只显示从所选订阅筛选器中选择的包含密钥保管库的订阅，你可在 Azure 门户标头的“目录 + 订阅”中选择这些订阅。

![订阅筛选器的屏幕截图](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>我想要在 Key Vault Insights 中进行更改或添加更多可视化效果，如何执行此操作

要进行更改，请选择“编辑模式”修改工作簿，然后可以将工作保存为绑定到指定订阅和资源组的新工作簿。

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>固定工作簿的任何部分后，时间粒度是多少

我们使用“自动”时间粒度，因此，它取决于选择的时间范围。

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>固定工作簿的任何部分后，时间范围是多少

时间范围将取决于面板设置。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>如果想要查看其他数据或生成自己的可视化效果，应该怎么做？ 如何对 Key Vault Insights 进行更改

可使用编辑模式来编辑现有工作簿，然后将工作保存为新的工作簿，该工作簿将包含所有新的更改。

## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](../visualize/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
