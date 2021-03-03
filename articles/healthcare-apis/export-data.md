---
title: 通过对 FHIR 的 Azure API 调用 $export 命令执行导出
description: 本文介绍如何使用 $export 导出 FHIR 数据
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 675030ac47cb26e817a9ef7ee51999f25020f292
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712686"
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

导出数据时，将为每个资源类型创建一个单独的文件。 为了确保导出的文件不会变得过大，我们会在导出文件大小超过 64 MB 后创建一个新的文件。 结果是，每个资源类型都可能会有多个文件， (即 ndjson、ndjson) 进行枚举。 


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

|查询参数        | 由 FHIR 规范定义？    |  说明|
|------------------------|---|------------|
| \_outputFormat | 是 | 目前支持三个值以与 FHIR Spec： application/FHIR + ndjson、application/ndjson 或仅 ndjson 对齐。 所有导出作业都将返回 `ndjson` ，并且传递的值不会影响代码行为。 |
| \_since | 是 | 允许你仅导出自提供时间以来修改的资源 |
| \_类别 | 是 | 允许您指定将包含哪些类型的资源。 例如， \_ type = 患者只返回患者资源|
| \_typefilter | 是 | 若要请求更精细的筛选，可以将 \_ typefilter 与 \_ type 参数一起使用。 _TypeFilter 参数的值是以逗号分隔的 FHIR 查询列表，可进一步限制结果 |
| \_容器 | 否 |  指定已配置的存储帐户中应将数据导出到其中的容器。 如果指定容器，则会将数据导出到名为的新文件夹中的容器。 如果未指定容器，则会将其导出到使用时间戳和作业 ID 的新容器。 |

## <a name="secure-export-to-azure-storage"></a>安全导出到 Azure 存储

用于 FHIR 的 Azure API 支持安全的导出操作。 运行安全导出的一种方法是允许与用于 FHIR 的 Azure API 关联的特定 IP 地址访问 Azure 存储帐户。 根据存储帐户与用于 FHIR 的 Azure API 位于同一位置还是不同的位置，配置将有所不同。

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>如果 Azure 存储帐户在不同的区域中

从门户中选择 Azure 存储帐户的 "网络" 边栏选项卡。 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure 存储网络设置。" lightbox="media/export-data/storage-networking.png":::
   
选择 "所选网络"，然后在 "防火墙添加 IP 范围" 部分下的 " **地址范围** " 框中指定 IP 地址， \| 以允许从 internet 或本地网络进行访问。 可在下表中找到用于预配 Azure API for FHIR 服务的 Azure 区域的 IP 地址。

|**Azure 区域**         |**公共 IP 地址** |
|:----------------------|:-------------------|
| 澳大利亚东部       | 20.53.44.80       |
| 加拿大中部       | 20.48.192.84      |
| 美国中部           | 52.182.208.31     |
| 美国东部              | 20.62.128.148     |
| 美国东部 2            | 20.49.102.228     |
| 美国东部 2 EUAP       | 20.39.26.254      |
| 德国北部        | 51.116.51.33      |
| 德国中西部 | 51.116.146.216    |
| Japan East           | 20.191.160.26     |
| 韩国中部        | 20.41.69.51       |
| 美国中北部     | 20.49.114.188     |
| 北欧         | 52.146.131.52     |
| 南非北部   | 102.133.220.197   |
| 美国中南部     | 13.73.254.220     |
| 东南亚       | 23.98.108.42      |
| 瑞士北部    | 51.107.60.95      |
| 英国南部             | 51.104.30.170     |
| 英国西部              | 51.137.164.94     |
| 美国中西部      | 52.150.156.44     |
| 西欧          | 20.61.98.66       |
| 美国西部 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>在同一区域中的 Azure 存储帐户

除了使用 CIDR 格式的特定 IP 地址范围（100.64.0.0/10）以外，配置过程与上面相同。 必须指定 IP 地址范围（包括100.64.0.0 –100.127.255.255）的原因，因为服务使用的实际 IP 地址因每个 $export 请求而异，但在范围内。

> [!Note] 
> 可以改为使用 10.0.2.0/24 范围内的专用 IP 地址。 在这种情况下，$export 操作不会成功。 可以重试 $export 请求，但不能保证下一次使用 100.64.0.0/10 范围内的 IP 地址。 这是设计的已知网络行为。 替代方法是在不同的区域中配置存储帐户。
    
## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 $export 命令导出 FHIR 资源。 接下来，了解如何导出已取消识别的数据：
 
>[!div class="nextstepaction"]
>[导出已取消识别的数据](de-identified-export.md)
