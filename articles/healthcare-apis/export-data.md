---
title: 通过对 FHIR 的 Azure API 调用 $export 命令执行导出
description: 本文介绍如何使用 $export 导出 FHIR 数据
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 8ad5ee78a525b3798bbf613168ff74a9e21fe99b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920251"
---
# <a name="how-to-export-fhir-data"></a>如何导出 FHIR 数据


大容量导出功能允许按照 [FHIR 规范](https://hl7.org/fhir/uv/bulkdata/export/index.html)从 FHIR 服务器导出数据。 

使用 $export 之前，需确保将用于 FHIR 的 Azure API 配置为使用该 API。 若要配置导出设置和创建 Azure 存储帐户，请参阅 [配置导出数据页](configure-export-data.md)。

## <a name="using-export-command"></a>使用 $export 命令

配置用于 FHIR 的 Azure API 以进行导出后，可以使用 $export 命令将数据导出到服务外。 数据将存储在配置导出时指定的存储帐户中。 若要了解如何在 FHIR server 中调用 $export 命令，请参阅 [HL7 FHIR $export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html)的文档。 

适用于 FHIR 的 Azure API 支持以下级别的 $export：
* [系统](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export)： `GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)： `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [患者 * 的组](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -Azure API for FHIR 导出所有相关资源，但不导出组的特征： `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export``Group/[ID]/$export`如果资源位于多个资源的隔离舱中或位于多个组中，则和可导出重复的资源。

此外，还支持在队列中通过 location 标头返回的 URL 来检查导出状态，同时还支持取消实际导出作业。

## <a name="settings-and-parameters"></a>设置和参数

### <a name="headers"></a>标头
$Export 作业中必须设置两个必需的标头参数。 这些值由当前 [$export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)定义。
* **Accept** -application/fhir + json
* **首选** -响应-异步

### <a name="query-parameters"></a>查询参数
适用于 FHIR 的 Azure API 支持以下查询参数。 所有这些参数都是可选的：

|查询参数        | 由 FHIR 规范定义？    |  描述|
|------------------------|---|------------|
| \_outputFormat | 是 | 目前支持三个值以与 FHIR Spec： application/FHIR + ndjson、application/ndjson 或仅 ndjson 对齐。 所有导出作业都将返回 `ndjson` ，并且传递的值不会影响代码行为。 |
| \_since | 是 | 允许你仅导出自提供时间以来修改的资源 |
| \_类别 | 是 | 允许您指定将包含哪些类型的资源。 例如， \_ type = 患者只返回患者资源|
| \_typefilter | 是 | 若要请求更精细的筛选，可以将 \_ typefilter 与 \_ type 参数一起使用。 _TypeFilter 参数的值是以逗号分隔的 FHIR 查询列表，可进一步限制结果 |
| \_container | 否 |  指定已配置的存储帐户中应将数据导出到其中的容器。 如果指定容器，则会将数据导出到名为的新文件夹中的容器。 如果未指定容器，则会将其导出到使用时间戳和作业 ID 的新容器。 |


## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 $export 命令导出 FHIR 资源。 接下来，了解如何导出已取消识别的数据：
 
>[!div class="nextstepaction"]
>[导出已取消识别的数据](de-identified-export.md)
