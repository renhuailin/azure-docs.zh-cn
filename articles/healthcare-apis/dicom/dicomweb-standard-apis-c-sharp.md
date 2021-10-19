---
title: 将 DICOMweb &trade; 标准 API 与 C# 一起使用 - Azure 医疗保健 API
description: 本教程介绍如何将 DICOMweb 标准 API 与 C# 一起使用。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: aersoy
ms.openlocfilehash: 2594ff27dd7e4bae5c5463c32a5d4ee3d481cfe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783126"
---
# <a name="using-dicomwebtrade-standard-apis-with-c"></a>将 DICOMweb &trade; 标准 API 与 C 一起使用#

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本教程使用 C# 演示如何使用 DICOM 服务。

在本教程中，我们将使用以下示例 [.dcm DICOM 文件](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)。

* blue-circle.dcm
* dicom-metadata.csv
* green-square.dcm
* red-triangle.dcm

示例 DICOM 文件的文件名、studyUID、seriesUID 和 instanceUID 如下所示：

| 文件 | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> 其中每个文件都代表单个实例，属于同一研究。 此外，绿色正方形和红色三角形属于同一系列，而蓝色圆圈位于单独的序列中。

## <a name="prerequisites"></a>先决条件

若要使用 DICOMweb &trade; 标准 API，必须部署 DICOM 服务的实例。 如果尚未部署 DICOM 服务的实例，请参阅使用 Azure 门户 部署 [DICOM 服务](deploy-dicom-services-in-azure.md)。

部署 DICOM 服务的实例后，检索应用服务的 URL：

1. 登录到 [Azure 门户](https://ms.portal.azure.com/)。
1. 搜索 **"最近使用** 的资源"并选择 DICOM 服务实例。
1. 复制 **DICOM** 服务的服务 URL。 在请求时，请确保将版本指定为 URL 的一部分。 有关详细信息，请参阅 [DICOM 服务的 API 版本控制文档](api-versioning-dicom-service.md)。

在应用程序中，安装以下NuGet包：

*  [DICOM 客户端](https://microsofthealthoss.visualstudio.com/FhirServer/_packaging?_a=package&feed=Public&package=Microsoft.Health.Dicom.Client&protocolType=NuGet)

*  [fo-dicom](https://www.nuget.org/packages/fo-dicom/)

## <a name="create-a-dicomwebclient"></a>创建 DicomWebClient

部署 DICOM 服务后，将创建一个 DicomWebClient。 运行以下代码片段以创建 DicomWebClient，本教程的其余部分将使用该代码片段。 确保已如前所述NuGet安装两个包。 如果尚未获取令牌，请参阅使用 Azure CLI 获取[DICOM 服务的访问Azure CLI。](dicom-get-access-token-azure-cli.md)

```c#
string webServerUrl ="{Your DicomWeb Server URL}"
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri(webServerUrl);
IDicomWebClient client = new DicomWebClient(httpClient);
client.HttpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", “{Your token value}”); 
```
使用 DicomWebClient，我们现在可以执行存储、检索、搜索和删除操作。

## <a name="store-dicom-instances-stow"></a>将 DICOM 实例存储在 (中) 

使用已创建的 DicomWebClient，我们现在可以存储 DICOM 文件。

### <a name="store-single-instance"></a>存储单个实例

存储单个实例演示如何上传单个 DICOM 文件。

_细节：_

* POST /studies

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To blue-circle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile });
```

### <a name="store-instances-for-a-specific-study"></a>存储特定研究的实例

存储特定研究的实例演示如何将 DICOM 文件上传到指定的研究。

_细节：_

* POST /study/{study}

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To red-triangle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile }, "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420");
```

在继续学习本教程的下一部分之前，使用上述任一 `green-square.dcm` 方法上传文件。

## <a name="retrieving-dicom-instances-wado"></a>检索 WADO ()  (DICOM) 

以下代码片段将演示如何使用之前创建的 DicomWebClient 执行每个检索查询。

以下变量将在整个示例中使用：

```c#
string studyInstanceUid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; //StudyInstanceUID for all 3 examples
string seriesInstanceUid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; //SeriesInstanceUID for green-square and red-triangle
string sopInstanceUid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; //SOPInstanceUID for red-triangle
```

### <a name="retrieve-all-instances-within-a-study"></a>检索研究内的所有实例

检索研究内的所有实例检索单个研究内的所有实例。

_细节：_

* GET /study/{study}

```c#
DicomWebResponse response = await client.RetrieveStudyAsync(studyInstanceUid);
```

我们之前上传的所有三个 dcm 文件都是同一研究一部分，因此响应应返回所有三个实例。 验证响应的状态代码是否正常，以及是否返回所有三个实例。

### <a name="use-the-retrieved-instances"></a>使用检索到的实例

以下代码片段演示如何访问检索到的实例。 它还演示如何访问实例的一些字段，以及如何将其另存为 dcm 文件。

```c#
DicomWebAsyncEnumerableResponse<DicomFile> response = await client.RetrieveStudyAsync(studyInstanceUid);
await foreach (DicomFile file in response)
{
    string patientName = file.Dataset.GetString(DicomTag.PatientName);
    string studyId = file.Dataset.GetString(DicomTag.StudyID);
    string seriesNumber = file.Dataset.GetString(DicomTag.SeriesNumber);
    string instanceNumber = file.Dataset.GetString(DicomTag.InstanceNumber);

    file.Save($"<path_to_save>\\{patientName}{studyId}{seriesNumber}{instanceNumber}.dcm");
}
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>检索研究中所有实例的元数据

此响应检索单个研究内所有实例的元数据。

_细节：_

* GET /study/{study}/metadata

```c#
DicomWebResponse response = await client.RetrieveStudyMetadataAsync(studyInstanceUid);
```

我们之前上传的所有三个 dcm 文件都是同一研究一部分，因此响应应返回所有三个实例的元数据。 验证响应的状态代码是否正常，以及是否返回了所有元数据。

### <a name="retrieve-all-instances-within-a-series"></a>检索序列内的所有实例

此响应检索单个系列内的所有实例。

_细节：_

* GET /study/{study}/series/{series}


```c#
DicomWebResponse response = await client.RetrieveSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

此系列有两个实例 (绿色正方形和红色三角形) ，因此响应应返回这两个实例。 验证响应的状态代码是否正常，以及是否返回了两个实例。

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>检索序列中所有实例的元数据

此响应检索单个研究内所有实例的元数据。

_细节：_

* GET /study/{study}/series/{series}/metadata

```c#
DicomWebResponse response = await client.RetrieveSeriesMetadataAsync(studyInstanceUid, seriesInstanceUid);
```

此序列有两个实例 (绿色正方形和红色三角形) ，因此响应应返回这两个实例的元数据。 验证响应的状态代码是否正常，以及是否返回了元数据的两个实例。

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>检索一系列研究中的单个实例

此请求检索单个实例。

_细节：_

* GET /study/{study}/series{series}/instances/{instance}

```c#
DicomWebResponse response = await client.RetrieveInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

此响应只应返回实例红色三角形。 验证响应的状态代码是否正常，以及是否返回了 实例。

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>检索一系列研究中的单个实例的元数据

此请求检索单个研究与系列中单个实例的元数据。

_细节：_

* GET /study/{study}/series/{series}/instances/{instance}/metadata

```c#
DicomWebResponse response = await client.RetrieveInstanceMetadataAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

此响应只应返回实例红三角形的元数据。 验证响应的状态代码是否正常，以及是否返回了元数据。

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>从单个实例中检索一个或多个帧

此请求从单个实例检索一个或多个帧。

_细节：_

* GET /study/{study}/series/{series}/instances/{instance}/frames/{frames}

```c#
DicomWebResponse response = await client.RetrieveFramesAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid, frames: new[] { 1 });

```

此响应应返回红色三角形中的唯一帧。 验证响应的状态代码是否正常，以及是否返回了帧。

## <a name="query-dicom-qido"></a>查询 DICOM (QIDO) 

> [!NOTE]
> 有关支持的 [DICOM 属性，请参阅 DICOM](dicom-services-conformance-statement.md#supported-search-parameters) 一致性声明。

### <a name="search-for-studies"></a>搜索检查

此请求按 DICOM 属性搜索一个或多个研究。

_细节：_

* GET /studies？StudyInstanceUID={study}

```c#
string query = $"/studies?StudyInstanceUID={studyInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

验证响应是否包含一个研究，以及响应代码是否正常。

### <a name="search-for-series"></a>搜索序列

此请求按 DICOM 属性搜索一个或多个序列。

_细节：_

* GET /series？SeriesInstanceUID={series}

```c#
string query = $"/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

验证响应是否包含一个序列，以及响应代码是否正常。

### <a name="search-for-series-within-a-study"></a>在研究内搜索序列

此请求通过 DICOM 属性在单个研究内搜索一个或多个序列。

_细节：_

* GET /study/{study}/series？SeriesInstanceUID={series}

```c#
string query = $"/studies/{studyInstanceUid}/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

验证响应是否包含一个序列，以及响应代码是否正常。

### <a name="search-for-instances"></a>搜索实例

此请求按 DICOM 属性搜索一个或多个实例。

_细节：_

* GET /instances？SOPInstanceUID={instance}

```c#
string query = $"/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

验证响应是否包含一个实例，以及响应代码是否正常。

### <a name="search-for-instances-within-a-study"></a>在研究内搜索实例

此请求通过 DICOM 属性在单个研究内搜索一个或多个实例。

_细节：_

* GET /study/{study}/instances？SOPInstanceUID={instance}

```c#
string query = $"/studies/{studyInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

验证响应是否包含一个实例，以及响应代码是否正常。

### <a name="search-for-instances-within-a-study-and-series"></a>在学习和系列中搜索实例

此请求按 DICOM 属性在单个研究内和单个序列中搜索一个或多个实例。

_细节：_

* GET /study/{study}/series/{series}实例？SOPInstanceUID={instance}

```c#
string query = $"/studies/{studyInstanceUid}/series/{seriesInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

验证响应是否包含一个实例，以及响应代码是否正常。

## <a name="delete-dicom"></a>删除 DICOM

> [!NOTE]
> 删除不是 DICOM 标准的一部分，但为方便起见已添加。

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>删除学习和系列中的特定实例

此请求删除单个学习和单个序列中的单个实例。

_细节：_

* DELETE /study/{study}/series/{series}/instances/{instance}

```c#
string sopInstanceUidRed = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395";
DicomWebResponse response = await client.DeleteInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUidRed);
```

此重新设置从服务器中删除红色三角形实例。 如果成功，则响应状态代码不包含任何内容。

### <a name="delete-a-specific-series-within-a-study"></a>删除学习中的特定系列

此请求将删除单个 (，并且所有子实例) 单个研究内。

_细节：_

* DELETE /study/{study}/series/{series}

```c#
DicomWebResponse response = await client.DeleteSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

此响应将删除绿色正方形实例 (它是从服务器中) 序列中唯一的元素。 如果成功，响应状态代码将不包含任何内容。

### <a name="delete-a-specific-study"></a>删除特定研究

此请求将删除单个 (以及所有子系列和实例) 。

_细节：_

* DELETE /study/{study}

```c#
DicomWebResponse response = await client.DeleteStudyAsync(studyInstanceUid);
```

此响应将删除蓝色圆圈 (该实例是该系列中从服务器) 的唯一元素。 如果成功，则响应状态代码不包含任何内容。

### <a name="next-steps"></a>后续步骤

有关 DICOM 服务的信息，请参阅

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)
