---
title: 管理指标顾问中的数据馈送
titleSuffix: Azure Cognitive Services
description: 了解如何管理已添加到指标顾问中的数据馈送。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: mbullwin
ms.openlocfilehash: dadefe25926b5a3e3c81cef21ff5d030692ccde4
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341118"
---
# <a name="how-to-manage-your-data-feeds"></a>如何：管理数据馈送

了解如何在指标顾问中管理已载入的数据馈送。 本文将指导你完成在指标顾问中管理数据馈送的步骤。

## <a name="edit-a-data-feed"></a>编辑数据馈送

> [!NOTE]
> 创建数据馈送后，无法更改以下详细信息。 
> * 数据馈送 ID
> * 创建时间
> * 维度
> * 源类型
> * 粒度

仅允许数据馈送的管理员对其进行更改。 

在数据馈送列表页上，可以“暂停”、“重新激活”、“删除”数据馈送：

* “暂停/重新激活”：选择“暂停/播放”按钮以暂停/重新激活数据馈送 。

* “删除”：选择“删除”按钮以删除数据馈送 。

如果更改引入开始时间，则需要再次验证架构。 可以通过在数据馈送详细信息页中单击“编辑”来对其进行更改。

##  <a name="backfill-your-data-feed"></a>回填数据馈送

选择“回填”按钮触发时间戳的即时引入，以修复失败的引入或替代现有数据。
- 开始时间为包含时间。
- 结束时间为非包含时间。
- 异常检测仅在所选范围内重新触发。

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="回填数据馈送":::

## <a name="manage-permission-of-a-data-feed"></a>管理数据馈送的权限

工作区访问权限由指标顾问资源控制，该资源使用 Azure Active Directory 进行身份验证。 另一层权限控制应用于指标数据。

利用指标顾问可以向不同的人员组授予针对不同数据馈送的权限。 有两种类型的角色： 

- 管理员：具有管理数据馈送的完全权限，包括修改和删除。
- 查看者：有权访问数据馈送的只读视图。
 

## <a name="advanced-settings"></a>高级设置

创建新的数据馈送时有几个可选的高级设置，可以在数据馈送详细信息页中修改这些设置。

### <a name="ingestion-options"></a>引入选项

* **引入时间偏移**：默认情况下，根据指定的粒度对数据进行引入。 例如，时间戳为每日的指标将在其时间戳的后一天引入。 可以通过偏移量使用正数延迟引入时间，或使用负数提前引入时间 。

* **最大并发**：如果数据源支持有限的并发，请设置此参数。 否则保留默认设置。

* **多长时间后停止重试**：如果数据引入失败，它将在一段时间内自动重试。 该时段的开始是发生第一次数据引入的时间。 该时段的长度根据粒度定义。 如果保留默认值 (-1)，则将根据粒度确定该值，如下所示。
    
    | 粒度       | 多长时间后停止重试           |
    | :------------ | :--------------- |
    | 每日、自定义（>= 1 天）、每周、每月、每年     | 7 天          |
    | 每小时、自定义（< 1 天）       | 72 小时 |

* **最小重试间隔**：重试从源拉取数据时，可以指定最小间隔。 如果保留默认值 (-1)，则将根据粒度确定重试间隔，如下所示。
    
    | 粒度       | 最小重试间隔           |
    | :------------ | :--------------- |
    | 每日、自定义（>= 1 天）、每周、每月     | 30 分钟          |
    | 每小时、自定义（< 1 天）      | 10 分钟 |
    | 每年 | 1 天          |
 
### <a name="fill-gap-when-detecting"></a>检测时填充间隙： 

> [!NOTE]
> 此设置不会影响数据源，也不会影响门户上显示的数据图表。 仅在异常检测期间进行自动填充。

有时系列并不连续。 当缺少数据点时，指标顾问将在异常检测之前使用指定的值填充这些点以提高准确性。
选项包括： 

* 使用以前的实际数据点的值。 这是默认设置。
* 使用特定值。

### <a name="action-link-template"></a>操作链接模板： 

操作链接模板用于预定义可操作的 HTTP URL，其中包含占位符 `%datafeed`、`%metric`、`%timestamp`、`%detect_config` 和 `%tagset`。 可以使用模板从异常或事件重定向到特定 URL 以向下钻取。

:::image type="content" source="../media/action-link-template.png" alt-text="操作链接模板" lightbox="../media/action-link-template.png":::

填写操作链接后，单击事件列表的操作选项上的“转到操作链接”，然后单击诊断树的右键单击菜单。 将操作链接模板中的占位符替换为异常或事件的相应值。

| 占位符 | 示例 | 评论 |
| ---------- | -------- | ------- |
| `%datafeed` | - | 数据馈送 ID |
| `%metric` | - | 指标 ID |
| `%detect_config` | - | 检测配置 ID |
| `%timestamp` | - | 持久事件的异常或结束时间的时间戳 |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | 事件的异常或顶级异常的维度值。   <br> `filterVal` 用于筛选掉方括号内的匹配值。   |

示例：

* 如果操作链接模板为 `https://action-link/metric/%metric?detectConfigId=%detect_config`：
  * 操作链接 `https://action-link/metric/1234?detectConfigId=2345` 将转到指标 `1234` 下的异常或事件，并检测配置 `2345`。

* 如果操作链接模板为 `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]`：
    * 当异常为 `{ "Dim1": "Val1", "Dim2": "Val2" }` 时，操作链接将为 `https://action-link?Dim1=Val1&Dim2=Val2`。 
    * 当异常为 `{ "Dim1": "", "Dim2": "Val2" }` 时，操作链接将为 `https://action-link?Dim2=Val2`，因为对于维度值为空的字符串将跳过 `[Dim1=***&]`。 

* 如果操作链接模板为 `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']`： 
    * 当异常为 `{ "Dim1": "Val1", "Dim2": "Val2" }` 时，操作链接将为 `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'`， 
    * 当异常为 `{ "Dim1": "", "Dim2": "Val2" }` 时，操作链接将为 `https://action-link?filter=Name/Dim2 eq 'Val2'`，因为对于维度值为空的字符串将跳过 `[Name/Dim1 eq '***' and ]`。 
   
### <a name="data-feed-not-available-alert-settings"></a>“数据馈送不可用”警报设置

如果从数据馈送开始引入之时起，在指定的宽限期内没有从源中引入任何数据，则数据馈送会被视为不可用。 这种情况会触发警报。

若要配置警报，需要先[创建挂钩](alerts.md#create-a-hook)。 警报将通过配置的挂钩发送。

* **宽限期**：“宽限期”设置用于确定在没有数据点引入时发送警报的时间。 参考点是第一次引入的时间。 如果引入失败，指标顾问将继续按照粒度指定的固定间隔进行尝试。 如果在宽限期后仍失败，则会发送警报。

* **自动推迟**：当此选项设置为零时，每个不可用的时间戳都将触发警报。 如果指定的设置不是零，则根据指定的设置，第一个不可用的时间戳之后的连续时间戳不会触发警报。

## <a name="next-steps"></a>后续步骤
- [配置指标并微调检测配置](configure-metrics.md)
- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-an-incident.md)。
