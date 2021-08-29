---
title: 通过在 FHIR 服务上调用 $export 命令来执行导出
description: 本文介绍如何使用 $export 导出 FHIR 数据
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/25/2021
ms.author: cavoeg
ms.openlocfilehash: e161834e7b0d503e508f02260a150237f8345342
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778654"
---
# <a name="how-to-export-fhir-data"></a>如何导出 FHIR 数据


批量导出功能允许按照 [FHIR 规范](https://hl7.org/fhir/uv/bulkdata/export/index.html)从 FHIR 服务器导出数据。 

使用 $export 之前，需要确保已将 FHIR 服务配置为使用它。 有关配置导出设置和创建 Azure 存储帐户的信息，请参阅[配置导出数据页](configure-export-data.md)。

## <a name="using-export-command"></a>使用 $export 命令

将 FHIR 服务配置为进行导出后，可以使用 $export 命令将数据从服务中导出。 数据将存储到配置导出时指定的存储帐户。 若要了解如何在 FHIR 服务器中调用 $export 命令，请参阅关于 [HL7 FHIR $export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html)的文档。


作业停滞在错误状态

在某些情况下，作业可能会停滞在错误状态。 尤其是在存储帐户权限未正确设置的情况下会发生这种情况。 验证导出是否成功的一种方式是检查存储帐户，看相应的容器（即 ndjson）文件是否存在。 如果它们不存在，并且没有其他导出作业正在运行，则当前作业有可能停滞在错误状态。 你应当发送取消请求来取消导出作业，并尝试将该作业再次重新排队。 处于错误状态的导出的默认运行时间为 10 分钟，然后它会停止并移动到新作业或重试导出。 

FHIR 服务在以下级别支持 $export：
* [系统](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export)：`GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)：`GET https://<<FHIR service base URL>>/Patient/$export>>`
* [患者组*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) - FHIR 服务会导出所有相关资源，但不导出组的特征：`GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

导出数据时，将为每种资源类型创建单独的文件。 为确保导出的文件不会太大， 我们会在导出的单个文件大于 64 MB 时新建一个文件。 结果是，对于每种资源类型，你可能会获得多个枚举的文件，即 Patient-1.ndjson、Patient-2.ndjson。 


> [!Note] 
> 如果资源位于多个资源的隔离舱，或位于多个组，则 `Patient/$export` 和 `Group/[ID]/$export` 可能会导出重复的资源。

此外，还支持在排队过程中使用位置标头返回的 URL 检查导出状态，以及取消实际的导出作业。

### <a name="exporting-fhir-data-to-adls-gen2"></a>将 FHIR 数据导出到 ADLS Gen2

目前，对于启用了 ADLS Gen2 的存储帐户，我们支持 $export，但有以下限制：

- 用户尚不能利用[分层命名空间](../../storage/blobs/data-lake-storage-namespace.md)；无法将导出指向容器内的特定子目录。 我们仅提供指向特定容器的功能（我们会在其中为每个导出新建文件夹）。
- 导出完成后，我们不会再将任何内容导出到该文件夹，因为指向同一容器的后续导出将位于新创建的文件夹内。


## <a name="settings-and-parameters"></a>设置和参数

### <a name="headers"></a>头文件
必须为 $export 作业设置两个必需的标头参数。 这些值由当前的 [$export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)定义。
* **接受** - application/fhir+json
* **首选** - respond-async

### <a name="query-parameters"></a>查询参数
FHIR 服务支持以下查询参数。 所有参数都是可选的：

|查询参数        | 由 FHIR 规范定义？    |  说明|
|------------------------|---|------------|
| \_outputFormat | 是 | 目前支持三个值，用于与 FHIR 规范对齐：application/fhir+ndjson、application/ndjson 或 ndjson。 导出的所有作业将返回 `ndjson`，并且传递的值对代码行为没有影响。 |
| \_since | 是 | 允许仅导出在所提供时间后修改的资源 |
| \_type | 是 | 允许指定要包含的资源类型。 例如，\_type=Patient 将仅返回患者资源|
| \_typefilter | 是 | 若要请求细粒度筛选，可以将 \_typefilter 与 \_type 参数结合使用。 _typeFilter 参数的值是以逗号分隔的 FHIR 查询的列表，这些查询进一步限制了结果 |
| \_container | 否 |  指定配置的存储帐户中的容器，数据应导出到其中。 如果指定了某个容器，则数据将导出到该容器中的某个文件夹。 如果未指定容器，则数据将导出到一个新容器。 |

> [!Note]
> 只允许将 FHIR 服务的存储帐户所在订阅中的存储帐户注册为 $export 操作的目标。

## <a name="secure-export-to-azure-storage"></a>安全导出到 Azure 存储

FHIR 服务支持安全导出操作。 请选择以下两个选项之一：

* 允许 FHIR 服务作为 Microsoft 信任的服务访问 Azure 存储帐户。
 
* 允许与 FHIR 服务关联的特定 IP 地址访问 Azure 存储帐户。 此选项提供两种不同的配置，具体取决于该存储帐户与 FHIR 服务的存储帐户位于同一位置还是不同的位置。

### <a name="allowing-fhir-service-as-a-microsoft-trusted-service"></a>允许 FHIR 服务作为 Microsoft 信任的服务

从 Azure 门户中选择一个存储帐户，然后选择“网络”边栏选项卡。 在“防火墙和虚拟网络”选项卡下选择“选定的网络”。 

> [!IMPORTANT]
> 确保已使用 FHIR 服务的托管标识向该服务授予了对存储帐户的访问权限。 有关更多详细信息，请参阅[配置导出设置并设置存储帐户](./configure-export-data.md)。

  :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure 存储网络设置。" lightbox="media/export-data/storage-networking.png":::

在“例外”部分，选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框，然后保存设置。 

:::image type="content" source="media/export-data/exceptions.png" alt-text="允许受信任的 Microsoft 服务访问此存储帐户。":::

你现在可以安全地将 FHIR 数据导出到存储帐户了。 请注意，存储帐户位于所选网络上，不可公开访问。 若要访问文件，可以启用并使用存储帐户的专用终结点，或在短时间内为存储帐户启用所有网络。

> [!IMPORTANT]
> 用户界面稍后会进行更新，以允许你选择 FHIR 服务的“资源类型”和一个特定的服务实例。

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-a-different-region"></a>允许另一区域中的 Azure 存储帐户的特定 IP 地址

从门户中选择 Azure 存储帐户的“网络”边栏选项卡。 
   
选择“所选网络”。 在“防火墙”部分的“地址范围”框中指定 IP 地址。 添加 IP 范围，以允许从 Internet 或本地网络进行访问。 可以在下表中为预配了 FHIR 服务的 Azure 区域查找 IP 地址。

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
> 以上步骤类似于“如何将数据转换为 FHIR（预览版）”文档中所述的配置步骤。 有关详细信息，请参阅[托管和使用模板](./convert-data.md#host-and-use-templates)。

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-the-same-region"></a>允许同一区域中的 Azure 存储帐户的特定 IP 地址

除改用 CIDR 格式的特定 IP 地址范围 100.64.0.0/10 以外，配置过程与上述过程相同。 必须指定 IP 地址范围（包括 100.64.0.0 - 100.127.255.255）的原因如下：对于每一个 $export 请求，服务使用的实际 IP 地址会有所不同，但会处于该范围内。

> [!Note] 
> 可以改用 10.0.2.0/24 范围内的专用 IP 地址。 在该情况下，$export 操作不会成功。 可以重试 $export 请求，但不保证下次会使用 100.64.0.0/10 范围内的 IP 地址。 这是众所周知的默认网络行为。 变通方法是在其他区域配置存储帐户。
    
## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何使用 $export 命令导出 FHIR 资源。 接下来，若要了解如何导出取消标识的数据，请参阅：
 
>[!div class="nextstepaction"]
>[导出去识别的数据](de-identified-export.md)