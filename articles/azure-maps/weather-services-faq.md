---
title: 'Microsoft Azure (预览版映射天气服务) 常见问题 (FAQ) '
description: 查找有关 Azure Maps 天气预报 (预览版) 数据和功能的常见问题的解答。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678123"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Azure Maps 天气服务 (预览版) 常见问题 (常见问题) 

> [!IMPORTANT]
> Azure Maps 天气服务目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文解答了有关 Azure Maps [天气服务](/rest/api/maps/weather) 数据和功能的常见问题。 包含以下主题：

* 数据源和数据模型
* 天气服务范围和可用性
* 数据更新频率
* Azure Maps Sdk 进行开发
* 用于可视化天气数据的选项，包括 Microsoft Power BI 集成

## <a name="data-sources-and-data-models"></a>数据源和数据模型

**Azure Maps 源天气数据的情况如何？**

Azure Maps 是通过世界一流的移动性和位置技术合作伙伴的协作而构建的，其中包括提供基础天气数据的 AccuWeather。 若要阅读与 AccuWeather 的 Azure Maps "协作的公告，请参阅 [Rain 或真知灼见： Azure Maps 天气服务将深入了解你的企业](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/)。

AccuWeather 在世界各地都提供实时天气和环境信息，这在很大程度上是由于其与众多国家政府天气机构的合作关系和其他专有的安排。 下面提供了此基础信息的列表。

* 来自政府机构的公开可用的全球表面观察
* 政府和私营公司的专有 surface 观察数据集
* 超过40国家/地区的高分辨率雷达数据
* 同类最佳实时全局闪电数据
* 针对超过60国家/地区和区域的政府颁发的天气警告
* 来自 geostationary 天气卫星的卫星数据涵盖了整个世界
* 超过150个数字预测模型，包括内部、专有建模、政府模型（如美国全球预测系统 (GFS) ，以及由专用公司提供的唯一 downscaled 模型）
* 空气质量观察
* 运输部门的观察值

与其他数据一起结合了成千上万个表面观察，以创建和影响用户可用的当前情况。 这不仅包括免费可用的标准数据集，而且还包括从许多国家/地区（包括印度、巴西、加拿大和其他专有输入）的国家种气象站服务获取的唯一观测值。 这些唯一数据集增加了用户的当前条件数据的空间和临时解决方案。 

这些数据集是实时查看的，它利用 AccuWeather 的专有人工智能算法持续修改预测，确保它们始终合并最新的数据，从而最大程度地提高其准确性。

**哪些模型创建天气预测数据？**

许多天气预测指南系统可用于表述全局预测。 每日使用超过150个数字预测模型，包括外部和内部数据集。 这包括政府模型，如欧洲中心 ECMWF 和美国全球预测系统 (GFS) 。 此外，AccuWeather 还合并了专用的高分辨率模型，缩减向特定位置和战略地区性域进行预测，以预测天气的准确性。 过去几年来，AccuWeather 的独特混合和加权算法已开发完毕。 这些算法可通过最佳方式利用众多预测输入来提供高度准确的预测。

## <a name="weather-services-preview-coverage-and-availability"></a>天气服务 (预览) 覆盖率和可用性

**对于不同的国家/地区，我可以期望哪种覆盖范围？**

天气服务范围因国家/地区而异。 所有功能在每个国家/地区不可用。 有关详细信息，请参阅 [覆盖范围文档](./weather-coverage.md)。

## <a name="data-update-frequency"></a>数据更新频率

**当前条件数据的更新频率是多少？**

当前条件数据大约每小时更新一次，但可以使用快速变化的条件更频繁地进行更新，例如，较大的温度变化、天空情况改变、降水量更改等。 随着条件的变化，世界上的大多数观察站都将每小时报告多次。 但是，某些区域在计划的时间间隔内仍将只更新一次、两次或四次。  

Azure Maps 将当前条件数据缓存多达10分钟，以帮助在数据发生时捕获数据的近乎实时的更新频率。 若要查看缓存的响应何时过期，并避免显示过时的数据，可以在 Azure Maps API 响应的 HTTP 标头中利用 Expires 标头信息。

**每日和每小时预测数据的更新频率是多少？**

每日和每小时预测数据每天更新多次，因为接收到更新的观测值。  例如，如果超过预测的高温/下限，我们的预测数据将在下一个更新周期进行调整。 这可能会在不同的间隔发生，但通常会在一小时内发生。 许多突然天气情况可能导致预测数据发生变化。 例如，在热夏季时间，会突然出现隔离的雷雨，使云覆盖和 rain 更高。 隔离风暴可以有效地将温度降到10度。 这一新的温度值将影响当天剩余时间的每小时和每日预测，因此将在数据集中更新。

Azure Maps 的预测 Api 缓存最多30分钟。 若要查看缓存的响应何时过期，并避免显示过时的数据，可以在 Azure Maps API 响应的 HTTP 标头中利用 Expires 标头信息。 建议根据特定的产品用例和 UI (用户界面) 根据需要进行更新。

## <a name="developing-with-azure-maps-sdks"></a>Azure Maps Sdk 进行开发

**Azure Maps Web SDK 是否以本机方式支持天气服务 (预览版) 集成？**

Azure Maps Web SDK 提供服务模块。 服务模块是帮助程序库，可轻松地使用 web 或 Node.js 应用程序中 Azure Maps REST 服务。 使用 JavaScript 或 TypeScript。 若要开始，请参阅 [文档](./how-to-use-services-module.md)。

**Azure Maps Android SDK 以本机方式支持天气服务 (预览) 集成吗？**

Azure Maps Android Sdk 支持 Mercator 图块层，这些图块可以具有 x/y/缩放表示法、四键表示法或 EPSG 3857 范围框表示法。

我们计划为 Java/Android 创建服务模块，此模块类似于 web SDK 模块。 Android 服务模块可让你轻松访问 Java 或 Android 应用程序中的所有 Azure Maps 服务。  

## <a name="data-visualizations"></a>数据可视化  

**Azure Maps 天气磁贴 Power BI 视觉支持 Azure Maps 吗？**

是的。 若要了解如何将雷达图和红外卫星磁贴迁移到 Microsoft Power BI 视觉对象，请参阅 [向 Power BI 视觉对象添加图块层](./power-bi-visual-add-tile-layer.md)。 

**如何实现解释用于雷达图和卫星磁贴的颜色？**

Azure Maps [天气概念文章](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) 包含有助于解释用于雷达图和卫星磁贴的颜色的指南。 本文介绍了颜色样本和十六进制颜色代码。
 
**能否创建雷达和卫星磁贴动画？**

是的。 除了实时雷达图和卫星磁贴外，Azure Maps 客户还可以请求过去和未来的磁贴，以利用地图叠加来增强数据可视化效果。 这可以通过直接调用 [获取地图磁贴 V2 API](/rest/api/maps/renderv2/getmaptilepreview) 或通过 AZURE MAPS web SDK 来请求磁贴来完成。 雷达图最多提供1.5 小时，未来最多可达2小时。 磁贴和在5分钟间隔内可用。 红外磁贴过去最多可提供3小时，并可按10分钟间隔提供。 有关详细信息，请参阅开源天气磁贴动画 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)。  

**是否为不同天气情况提供图标？**

是的。 可在 [此处](./weather-services-concepts.md#weather-icons)找到图标及其相应的代码。 请注意，只有部分天气服务 (预览) Api，例如  [获取当前条件 API](/rest/api/maps/weather/getcurrentconditionspreview)，返回响应中的 *iconCode* 。 有关详细信息，请参阅当前 WeatherConditions 开源 [代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)。

## <a name="next-steps"></a>后续步骤

如果此 FAQ 不能回答您的问题，您可以通过以下渠道 (与我们联系) ：

* 本文评论部分。
* [MSFT Q&Azure Maps 的页面](/answers/topics/azure-maps.html)。
* Microsoft 支持部门。 若要创建新的支持请求，请在 " [Azure 门户](https://portal.azure.com/)中的" 帮助 "选项卡上，选择" **帮助 +** 支持 "按钮，然后选择" **新建支持请求**"。
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) 提交功能请求。

了解如何使用 Azure Maps 天气服务 (预览版) 请求实时和预测天气数据：
> [!div class="nextstepaction"]
> [请求实时天气数据 ](how-to-request-weather-data.md)

Azure Maps 天气服务 (预览版) 概念文章：
> [!div class="nextstepaction"]
> [天气服务概念](weather-coverage.md)

浏览 Azure Maps 天气服务 (预览版) API 文档：

> [!div class="nextstepaction"]
> [Azure Maps 天气服务](/rest/api/maps/weather)