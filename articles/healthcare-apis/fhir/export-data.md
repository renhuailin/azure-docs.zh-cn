---
title: 通过在 Azure API for FHIR 上调用 $export 命令来执行导出
description: 本文介绍如何使用 $export 导出 FHIR 数据
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/25/2021
ms.author: cavoeg
ms.openlocfilehash: 54dadb47018b474bb7651ddb17b0170a2c07e29a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110477838"
---
# <a name="how-to-export-fhir-data"></a>如何导出 FHIR 数据


批量导出功能允许按照 [FHIR 规范](https://hl7.org/fhir/uv/bulkdata/export/index.html)从 FHIR 服务器导出数据。 

使用 $export 之前，需确保将用于 FHIR 的 Azure API 配置为使用该 API。 有关配置导出设置和创建 Azure 存储帐户的信息，请参阅[配置导出数据页](configure-export-data.md)。

## <a name="using-export-command"></a>使用 $export 命令

配置 Azure API for FHIRI 进行导出后，可以使用 $export 命令将数据从服务中导出。 数据将存储到配置导出时指定的存储帐户。 若要了解如何在 FHIR 服务器中调用 $export 命令，请参阅关于 [HL7 FHIR $export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html)的文档。


**作业停滞在错误状态**

在某些情况下，作业可能会停滞为错误状态。 如果未正确设置存储帐户权限，则会发生这种情况。 验证导出是否成功的一种方法是检查存储帐户，以查看相应的容器 (也就是说，ndjson) 文件是否存在。 如果它们不存在，而且没有其他运行的导出作业，则当前作业可能会停滞在错误状态。 你应通过发送取消请求来取消导出作业，并再次尝试重新排队作业。 处于错误状态的导出的默认运行时间是10分钟后，它将停止并移动到新作业或重试导出。 

Azure API For FHIR 在以下级别中支持 $export：
* [系统](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export)：`GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)：`GET https://<<FHIR service base URL>>/Patient/$export>>`
* [患者 * 的组](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -Azure API for FHIR 导出所有相关资源，但不导出组的特征： `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

导出数据时，将为每种资源类型创建单独的文件。 确保导出的文件不会变得过大。 在一个导出文件的大小达到 64 MB 后，我们创建一个新文件。 结果是，每个资源类型都可能会有多个文件， (即患者-ndjson、ndjson) 。 


> [!Note] 
> 如果资源位于多个资源的隔离舱，或位于多个组，则 `Patient/$export` 和 `Group/[ID]/$export` 可能会导出重复的资源。

此外，还支持在排队过程中使用位置标头返回的 URL 检查导出状态，以及取消实际的导出作业。

### <a name="exporting-fhir-data-to-adls-gen2"></a>将 FHIR 数据导出到 ADLS Gen2

目前，对于启用了 ADLS Gen2 的存储帐户，我们支持 $export，但有以下限制：

- 用户无法利用 [分层命名空间](../../storage/blobs/data-lake-storage-namespace.md)，但无法将导出定向到容器中的特定子目录。 我们仅提供指向特定容器的功能（我们会在其中为每个导出新建文件夹）。
- 导出完成后，我们不会再将任何内容导出到该文件夹，因为指向同一容器的后续导出将位于新创建的文件夹内。


## <a name="settings-and-parameters"></a>设置和参数

### <a name="headers"></a>头文件
必须为 $export 作业设置两个必需的标头参数。 这些值由当前的 [$export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)定义。
* **接受** - application/fhir+json
* **首选** - respond-async

### <a name="query-parameters"></a>查询参数
Azure API for FHIR 支持以下查询参数。 所有参数都是可选的：

|查询参数        | 由 FHIR 规范定义？    |  说明|
|------------------------|---|------------|
| \_outputFormat | 是 | 目前支持三个值，用于与 FHIR 规范对齐：application/fhir+ndjson、application/ndjson 或 ndjson。 导出的所有作业将返回 `ndjson`，并且传递的值对代码行为没有影响。 |
| \_since | 是 | 允许仅导出在所提供时间后修改的资源 |
| \_type | 是 | 允许指定要包含的资源类型。 例如，\_type=Patient 将仅返回患者资源|
| \_typefilter | 是 | 若要请求细粒度筛选，可以将 \_typefilter 与 \_type 参数结合使用。 _typeFilter 参数的值是以逗号分隔的 FHIR 查询的列表，这些查询进一步限制了结果 |
| \_container | 否 |  指定配置的存储帐户中的容器，数据应导出到其中。 如果指定容器，则会将数据导出到该容器中的文件夹。 如果未指定容器，则会将数据导出到新的容器。 |

> [!Note]
> 仅允许将与用于 FHIR 的 Azure API 相同的订阅中的存储帐户注册为 $export 操作的目标。

## <a name="secure-export-to-azure-storage"></a>安全导出到 Azure 存储

Azure API for FHIR 支持安全导出操作。 选择以下两个选项之一：

* 允许 FHIR 的 Azure API 作为 Microsoft 可信服务访问 Azure 存储帐户。
 
* 允许与用于 FHIR 的 Azure API 关联的特定 IP 地址访问 Azure 存储帐户。 此选项提供两种不同的配置，具体取决于存储帐户是位于与 存储帐户位于同一位置，还是与存储帐户位于Azure API for FHIR。

### <a name="allowing-azure-api-for-fhir-as-a-microsoft-trusted-service"></a>允许Azure API for FHIR Microsoft 受信任的服务

从"存储帐户"中选择Azure 门户，然后选择"网络 **"边栏** 选项卡。 在 **"防火墙和****虚拟网络"选项卡下选择"所选网络**"。

> [!IMPORTANT]
> 确保已使用托管标识向存储帐户授予Azure API for FHIR访问权限。 有关详细信息，请参阅 [配置导出设置并设置存储帐户](https://docs.microsoft.com/azure/healthcare-apis/fhir/configure-export-data)。

  :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure 存储网络设置。" lightbox="media/export-data/storage-networking.png":::

在" **例外"** 部分下，选择" **允许受信任的Microsoft 服务访问此** 存储帐户并保存设置。 

:::image type="content" source="media/export-data/exceptions.png" alt-text="允许受信任的Microsoft 服务访问此存储帐户。":::

现在可以安全地将 FHIR 数据导出到存储帐户。 请注意，存储帐户位于所选网络上，不可公开访问。 若要访问文件，可以启用和使用存储帐户的专用终结点，或在短时间内为存储帐户启用所有网络。

> [!IMPORTANT]
> 稍后将更新用户界面，以允许你选择特定服务实例Azure API for FHIR资源类型。

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-a-different-region"></a>允许不同区域 Azure 存储帐户的特定 IP 地址

从 **门户** 中选择"Azure 存储帐户的网络"。 
   
选择“所选网络”。 在"防火墙"部分下，在"地址范围"框中 **指定 IP** 地址。 添加 IP 范围以允许从 Internet 或本地网络访问。 可以在下表中查找预配了托管服务的 Azure Azure API for FHIR IP 地址。

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

> [!NOTE]
> 上述步骤类似于如何将数据转换为 FHIR (预览版) 。 有关详细信息，请参阅 [托管和使用模板](https://docs.microsoft.com/azure/healthcare-apis/fhir/convert-data#host-and-use-templates)

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-the-same-region"></a>允许同一区域中的 Azure 存储帐户具有特定 IP 地址

除改用 CIDR 格式的特定 IP 地址范围 100.64.0.0/10 以外，配置过程与上述过程相同。 必须指定 IP 地址范围（包括 100.64.0.0 - 100.127.255.255）的原因如下：对于每一个 $export 请求，服务使用的实际 IP 地址会有所不同，但会处于该范围内。

> [!Note] 
> 可以改用 10.0.2.0/24 范围内的专用 IP 地址。 在这种情况下，$export 操作不会成功。 可以重试 $export 请求，但不能保证下一次使用 100.64.0.0/10 范围内的 IP 地址。 这是众所周知的默认网络行为。 变通方法是在其他区域配置存储帐户。
    
## <a name="next-steps"></a>后续步骤

在本文中，已了解如何使用 $export 命令导出 FHIR 资源。 接下来，若要了解如何导出已取消识别的数据，请参阅：
 
>[!div class="nextstepaction"]
>[导出去识别的数据](de-identified-export.md)
