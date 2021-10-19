---
title: 通过卷使用 DICOMweb &trade; 标准 api-Azure 医疗保健 api
description: 在本教程中，你将了解如何将 DICOMweb 标准 Api 与卷一起使用。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: afa4b294b71a3cde198001d204d4670cf8ca1ec1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783182"
---
# <a name="using-dicomwebtrade-standard-apis-with-curl"></a>将 DICOMWeb &trade; 标准 api 用于卷

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本教程使用卷，演示如何使用 DICOM 服务。

在本教程中，我们将使用下面的 [示例 DCM DICOM 文件](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)。

* blue-circle dcm
* dicom-metadata.csv
* green-square dcm
* red-triangle dcm 

示例 DICOM 文件的文件名、studyUID、seriesUID 和 instanceUID 如下所示：

| 文件 | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

>[!NOTE]
>其中每个文件代表一个实例，并属于同一学习。 此外，绿色正方形和红色三角形是同一系列的一部分，而蓝色圆圈位于单独的系列中。

## <a name="prerequisites"></a>先决条件

若要使用 DICOMWeb &trade; 标准 api，必须已部署 DICOM 服务的实例。 如果尚未部署 DICOM 服务的实例，请参阅 [使用 Azure 门户部署 dicom 服务](deploy-dicom-services-in-azure.md)。

部署 DICOM 服务的实例后，请检索应用服务的 URL：

1. 登录到 [Azure 门户](https://ms.portal.azure.com/)。
2. 搜索 **最新资源** 并选择 DICOM 服务实例。
3. 复制 DICOM 服务的 **服务 URL** 。 
4. 如果尚未获得令牌，请参阅 [使用 Azure CLI 获取 DICOM 服务的访问令牌](dicom-get-access-token-azure-cli.md)。 

在此代码中，我们将访问公共预览版 Azure 服务。 重要的是，不要) 上传任何私有运行状况信息 (PHI。


## <a name="working-with-the-dicom-service"></a>使用 DICOM 服务
 
DICOMweb &trade; 标准使用 `multipart/related` 与 DICOM 特定 accept 标头结合使用的 HTTP 请求。 熟悉其他基于 REST 的 Api 的开发人员通常会发现，使用 DICOMweb &trade; 标准非常笨拙。 但是，一旦您已启动并运行，就很容易使用了。 只需稍微熟悉入门即可。

每个卷中的命令至少包含一个，有时必须替换两个变量。 若要简化命令的运行，请通过将以下变量替换为特定值来对其进行搜索和替换：

* {Service URL}此 URL 用于访问在 Azure 门户中预配的 DICOM 服务，例如 ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 。 发出请求时，请确保将版本指定为 url 的一部分。 有关详细信息，请参阅 [DICOM 服务的 API 版本控制文档](api-versioning-dicom-service.md)。
* {dicoms}-指向包含 red-triangle 文件的目录的路径，例如 `C:/dicom-server/docs/dcms`
    * 请确保使用正斜杠作为分隔符，并在没有尾随正斜杠的 _情况下_ 结束目录。


## <a name="uploading-dicom-instances-stow"></a> (船舶上传 DICOM 实例) 

### <a name="store-instances-using-multipartrelated"></a>存储-实例-使用多部分/相关

此请求旨在演示如何使用多部分/相关的上传 DICOM 文件。 

>[!NOTE]
>DICOM 服务比 DICOM 标准宽松更多。 但下面的示例演示了严格符合标准的 POST 请求。

_详细_

* 路径：。/studies
* 方法：POST
* 标头：
    * Accept： application/dicom + json
    * Content-type：多部分/相关;type = "application/dicom"
    * 授权：持有者 {令牌值}
* 正文：
    * Content-type：每个上传的文件的 application/dicom，由边界值分隔

某些编程语言和工具的行为方式不同。 例如，某些要求您定义自己的边界。 对于这种情况，可能需要使用略微修改的 Content-type 标头。 已成功使用以下项。
* Content-type：多部分/相关;type = "application/dicom";边界 = ABCD1234
* Content-type：多部分/相关;边界 = ABCD1234
* 内容类型：多部分/相关

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\""
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/red-triangle.dcm;type=application/dicom"
--trace-ascii "trace.txt"
```

### <a name="store-instances-for-a-specific-study"></a>存储特定研究的实例

此请求演示如何使用多部分/与指定的研究上载 DICOM 文件。

_详细_
* 路径：。/studies/{study}
* 方法：POST
* 标头：
    * Accept： application/dicom + json
    * Content-type：多部分/相关;type = "application/dicom"
    * 授权：持有者 {令牌值}
* 正文：
    * Content-type：每个上传的文件的 application/dicom，由边界值分隔

某些编程语言和工具的行为方式不同。 例如，某些要求您定义自己的边界。 对于这种情况，可能需要使用略微修改的 Content-type 标头。 已成功使用以下项。

 * Content-type：多部分/相关;type = "application/dicom";边界 = ABCD1234
 * Content-type：多部分/相关;边界 = ABCD1234
 * 内容类型：多部分/相关

```
curl --request POST "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\"
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/blue-circle.dcm;type=application/dicom"
```

### <a name="store-single-instance"></a>存储单实例

> [!NOTE]
> 这是一个非标准 API，它允许上传单个 DICOM 文件，而无需为多部分/相关内容配置 POST。 尽管曲线处理很多部分/相关，但此 API 允许 Postman 等工具将文件上传到 DICOM 服务。

上传单个 DICOM 文件需要以下方法。

_详细_
* 路径：。/studies
* 方法：POST
* 标头：
   * Accept： application/dicom + json
   * Content-Type：application/dicom
   * 授权：Bearer {token value}
* 正文：
    * 包含单个 DICOM 文件作为二进制字节。

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: application/dicom"
--header "Authorization: Bearer {token value}"
--data-binary "@{path-to-dicoms}/green-square.dcm"
```

## <a name="retrieving-dicom-wado"></a>检索 WADO (DICOM) 

### <a name="retrieve-all-instances-within-a-study"></a>检索研究内的所有实例

此请求检索单个研究内的所有实例，并返回它们作为多部分/相关字节的集合。

_细节：_
* 路径：。。/study/{study}
* 方法：GET
* 标头：
   * 接受：multipart/related;type="application/dicom";transfer-syntax=*
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

此 cURL 命令将在输出文件 (suppressWarnings.txt) 中显示下载的字节，但这些字节不是直接 DICOM 文件，只是多部分/相关下载的文本表示形式。

### <a name="retrieve-metadata-of-all-instances-in-study"></a>检索研究中所有实例的元数据

此请求检索单个研究内所有实例的元数据。

_细节：_
* 路径：。。/study/{study}/metadata
* 方法：GET
* 标头：
   * 接受：application/dicom+json
   * 授权：Bearer {token value}

此 cURL 命令将在输出文件 (suppressWarnings.txt) 中显示下载的字节，但这些字节不是直接 DICOM 文件，只是多部分/相关下载的文本表示形式。

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-all-instances-within-a-series"></a>检索序列内的所有实例

此请求检索单个序列内的所有实例，并返回它们作为多部分/相关字节的集合。

_细节：_
* 路径：。。/study/{study}/series/{series}
* 方法：GET
* 标头：
   * 接受：multipart/related;type="application/dicom";transfer-syntax=*
   * 授权：Bearer {token value}

此 cURL 命令将在输出文件 (suppressWarnings.txt) 中显示下载的字节，但它不是 DICOM 文件，只是多部分/相关下载的文本表示形式。

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>检索序列中所有实例的元数据

此请求检索单个研究内所有实例的元数据。

_细节：_
* 路径：。。/study/{study}/series/{series}/metadata
* 方法：GET
* 标头：
   * 接受：application/dicom+json
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```
 
### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>检索一系列研究中的单个实例

此请求检索单个实例，并返回为 DICOM 格式的字节流。

_细节：_
* 路径：。。/study/{study}/series{series}/instances/{instance}
* 方法：GET
* 标头：
   * 接受：application/dicom;transfer-syntax=*
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>检索一系列研究中的单个实例的元数据

此请求检索单个研究与系列中单个实例的元数据。

_细节：_
* 路径：。。/study/{study}/series/{series}/instances/{instance}/metadata
* 方法：GET
* 标头：
  * 接受：application/dicom+json
  * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>从单个实例中检索一个或多个帧

此请求从单个实例中检索一个或多个帧，并作为多部分/相关字节的集合返回这些帧。 可以通过传递以逗号分隔的帧数列表来检索多个帧。  所有具有图像的 DICOM 实例都至少具有一个帧，这通常只是与实例本身关联的图像。

_细节：_
* 路径：。。/study/{study}/series{series}/instances/{instance}/frames/1，2，3
* 方法：GET
* 标头：
   * 接受：multipart/related;type="application/octet-stream";transfer-syntax=1.2.840.10008.1.2.1 (Default) 或
   * 接受：multipart/related;type="application/octet-stream";transfer-syntax=* 或
   * 接受：multipart/related;type="application/octet-stream";
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/frames/1"
--header "Accept: multipart/related; type=\"application/octet-stream\"; transfer-syntax=1.2.840.10008.1.2.1"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

## <a name="query-dicom-qido"></a>查询 DICOM (QIDO) 

在下面的示例中，我们将使用项的唯一标识符搜索项。 还可以搜索其他属性，例如 `PatientName` 。

### <a name="search-for-studies"></a>搜索检查

此请求允许按 DICOM 属性搜索一个或多个研究。

有关支持的 DICOM 属性详细信息，请参阅 [DICOM 一致性语句](dicom-services-conformance-statement.md)。

_细节：_
* 路径：。。/studies？StudyInstanceUID={study}
* 方法：GET
* 标头：
   * 接受：application/dicom+json
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies?StudyInstanceUID=1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series"></a>搜索序列

此请求允许按 DICOM 属性搜索一个或多个序列。

有关支持的 DICOM 属性详细信息，请参阅 [DICOM 一致性语句](dicom-services-conformance-statement.md)。

_细节：_
* 路径：。。/series？SeriesInstanceUID={series}
* 方法：GET
* 标头：
   * 接受：application/dicom+json
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series-within-a-study"></a>在研究内搜索序列

此请求允许通过 DICOM 属性在单个研究内搜索一个或多个序列。

有关支持的 DICOM 属性详细信息，请参阅 [DICOM 一致性语句](dicom-services-conformance-statement.md)。

_细节：_
* 路径：。。/study/{study}/series？SeriesInstanceUID={series}
* 方法：GET
* 标头：
   * 接受：application/dicom+json
   * 授权：Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances"></a>搜索实例

此请求允许按 DICOM 属性搜索一个或多个实例。

有关支持的 DICOM 属性的详细信息，请参阅 [DICOM 一致性声明](dicom-services-conformance-statement.md)。

_详细_
* 路径：。实例?SOPInstanceUID = {instance}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 {令牌值}

```
curl --request GET "{Service URL}/v{version}/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study"></a>搜索研究中的实例

此请求支持通过 DICOM 属性在单个研究中搜索一个或多个实例。

有关支持的 DICOM 属性的详细信息，请参阅 [DICOM 一致性声明](dicom-services-conformance-statement.md)。

_详细_
* 路径：。/studies/{study}/instances?SOPInstanceUID = {instance}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 {令牌值} 

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study-and-series"></a>搜索研究和系列中的实例

此请求允许在单个研究中搜索一个或多个实例，并按 DICOM 属性搜索单个序列。

有关支持的 DICOM 属性的详细信息，请参阅 [DICOM 一致性声明](dicom-services-conformance-statement.md)

_详细_
* 路径：。/studies/{study}/series/{series}/instances?SOPInstanceUID = {instance}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 {令牌值}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```


## <a name="delete-dicom"></a>删除 DICOM 

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>删除研究和系列中的特定实例

此请求将删除单个研究和单个序列内的单个实例。

删除不属于 DICOM 标准，但为了方便起见，已添加。

_详细_
* 路径：。/studies/{study}/series/{series}/instances/{instance}
* 方法： DELETE
* 标头：
   * 授权：持有者 {令牌值}  

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-series-within-a-study"></a>删除学习中的特定系列

此请求将删除单个序列 (以及单个研究中) 的所有子实例。

删除不属于 DICOM 标准，但为了方便起见，已添加。

_详细_
* 路径：。/studies/{study}/series/{series}
* 方法： DELETE
* 标头：
   * 授权：持有者 {令牌值}

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-study"></a>删除特定学习

此请求将删除单个研究 (并且) 的所有子系列和实例。

删除不属于 DICOM 标准，但为了方便起见，已添加。

_详细_
* 路径：。/studies/{study}
* 方法： DELETE
* 标头：
   * 授权：持有者 {令牌值}

```
curl--request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498
--header "Authorization: Bearer {token value}"
```

### <a name="next-steps"></a>后续步骤

有关 DICOM 服务的信息，请参阅

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)
