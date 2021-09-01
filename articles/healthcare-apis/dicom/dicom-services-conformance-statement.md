---
title: Azure Healthcare APIs 的 DICOM 一致性声明
description: 本文档详细说明了有关 Azure Healthcare APIs 的 DICOM 一致性声明。
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: 8a43c48108c5714a0c11b08cf28b0fade1729596
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778604"
---
# <a name="dicom-conformance-statement"></a>DICOM 一致性声明

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

DICOM 服务的 Azure API 支持 DICOMweb&trade; 标准的子集。 此支持包括：

* [存储 (STOW-RS)](#store-stow-rs)
* [检索 (WADO-RS)](#retrieve-wado-rs)
* [搜索 (QIDO-RS)](#search-qido-rs)

此外，还支持以下非标准 API：

- [删除](#delete)

我们的服务还利用了 REST API 版本控制。 有关如何在发出请求时指定版本的信息，请访问 [DICOM 服务文档的 API 版本控制](api-versioning-dicom-service.md)。

## <a name="store-stow-rs"></a>存储 (STOW-RS)

此事务使用 POST 方法存储请求有效负载中包含的检查、序列和实例的表示形式。

| 方法 | 路径               | 说明 |
| :----- | :----------------- | :---------- |
| POST   | ../studies         | 存储实例。 |
| POST   | ../studies/{study} | 存储特定检查的实例。 |

参数 `study` 对应 DICOM 属性 StudyInstanceUID。 如果指定此项，则将拒绝不属于所提供调查的任何实例，同时出现警告代码 `43265`。

支持以下 `Accept` 响应标头：

* `application/dicom+json`

支持以下 `Content-Type` 标头：

* `multipart/related; type="application/dicom"`
* `application/dicom`

> [!NOTE]
> 服务器不会强制或替换与现有数据冲突的属性。 所有数据将按所提供的方式存储。

尝试存储的每个 DICOM 文件中需要存在以下 DICOM 元素：

* StudyInstanceUID
* SeriesInstanceUID
* SOPInstanceUID
* SOPClassUID
* PatientID

> [!NOTE]
> 所有标识符的长度必须介于 1 到 64 个字符之间，并且只能包含字母、数字或以下特殊字符：“.”和“-”。

存储的每个文件必须具有 StudyInstanceUID、SeriesInstanceUID 和 SopInstanceUID 的唯一组合。 如果已存在具有相同标识符的文件，则将返回警告代码 `45070`。

DICOM 文件大小限制：默认情况下，一个 DICOM 文件的大小限制为 2 GB。

### <a name="store-response-status-codes"></a>存储响应状态代码

| 代码                         | 说明 |
| :--------------------------- | :---------- |
| 200 (正常)                     | 请求中的所有 SOP 实例已存储。 |
| 202 (已接受)               | 请求中的某些实例已存储，但其他实例存储失败。 |
| 204 (无内容)             | 存储事务请求中未提供任何内容。 |
| 400（错误的请求）            | 请求的格式不正确。 例如，所提供的检查实例标识符不符合预期的 UID 格式。 |
| 401（未授权）           | 客户端未经身份验证。 |
| 403（已禁止）              | 用户未获得授权。 |
| 406（不可接受）         | 不支持指定的 `Accept` 标头。 |
| 409（冲突）               | 未存储存储事务请求中的任何实例。 |
| 415（媒体类型不受支持） | 不支持提供的 `Content-Type`。 |
| 503（服务不可用）    | 服务不可用或正忙。 请稍后再试。 |

### <a name="store-response-payload"></a>存储响应有效负载

响应有效负载将使用以下元素填充 DICOM 数据集：

| 标记          | 名称                  | 说明 |
| :----------- | :-------------------- | :---------- |
| (0008, 1190) | RetrieveURL           | 如果存储请求中提供了 StudyInstanceUID，并且至少有一个实例成功存储，则检索该检查的 URL。 |
| (0008, 1198) | FailedSOPSequence     | 未能存储的实例的序列。 |
| (0008, 1199) | ReferencedSOPSequence | 已存储实例的序列。 |

`FailedSOPSequence` 中的每个数据集都将具有以下元素（如果尝试存储的 DICOM 文件可读取）：

| 标记          | 名称                     | 说明 |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | 未能存储的实例的 SOP 类唯一标识符。 |
| (0008, 1150) | ReferencedSOPInstanceUID | 未能存储的实例的 SOP 例实唯一标识符。 |
| (0008, 1197) | FailureReason            | 此实例未能存储的原因代码。 |

`ReferencedSOPSequence` 中的每个数据集都包含以下元素：

| 标记          | 名称                     | 说明 |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | 未能存储的实例的 SOP 类唯一标识符。 |
| (0008, 1150) | ReferencedSOPInstanceUID | 未能存储的实例的 SOP 例实唯一标识符。 |
| (0008, 1190) | RetrieveURL              | DICOM 服务器上此实例的检索 URL。 |

以下是具有 `Accept` 标头 `application/dicom+json` 的示例响应：

```json
{
  "00081190":
  {
    "vr":"UR",
    "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232"]
  },
  "00081198":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI","Value":["cd70f89a-05bc-4dab-b6b8-1f3d2fcafeec"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["22c35d16-11ce-43fa-8f86-90ceed6cf4e7"]
      },
      "00081197":
      {
        "vr":"US",
        "Value":[43265]
      }
    }]
  },
  "00081199":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI",
        "Value":["d246deb5-18c8-4336-a591-aeb6f8596664"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      },
      "00081190":
      {
        "vr":"UR",
        "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232/series/8c4915f5-cc54-4e50-aa1f-9b06f6e58485/instances/4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      }
    }]
  }
}
```

### <a name="store-failure-reason-codes"></a>存储失败原因代码

| 代码  | 说明 |
| :---- | :---------- |
| 272   | 由于处理操作时出现一般错误，存储事务未能存储该实例。 |
| 43264 | DICOM 实例验证失败。 |
| 43265 | 所提供实例的 StudyInstanceUID 与存储请求中指定的 StudyInstanceUID 不匹配。 |
| 45070 | 已存储具有相同 StudyInstanceUID、SeriesInstanceUID 和 SopInstanceUID 的 DICOM 实例。 若希望更新内容，请先删除此实例。 |
| 45071 | 另一个进程正在创建 DICOM 实例，或以前的创建尝试失败且清理进程还没有机会进行清理。 请先删除此实例，然后再尝试重新创建。 |

## <a name="retrieve-wado-rs"></a>检索 (WADO-RS)

此检索事务为按引用检索存储的检查、序列、实例和帧提供支持。

| 方法 | 路径                                                                    | 说明 |
| :----- | :---------------------------------------------------------------------- | :---------- |
| GET    | ../studies/{study}                                                      | 检索检查中的所有实例。 |
| GET    | ../studies/{study}/metadata                                             | 检索检查中所有实例的元数据。 |
| GET    | ../studies/{study}/series/{series}                                      | 检索序列中的所有实例。 |
| GET    | ../studies/{study}/series/{series}/metadata                             | 检索序列中所有实例的元数据。 |
| GET    | ../studies/{study}/series/{series}/instances/{instance}                 | 检索单个实例。 |
| GET    | ../studies/{study}/series/{series}/instances/{instance}/metadata        | 检索单个实例的元数据。 |
| GET    | ../studies/{study}/series/{series}/instances/{instance}/frames/{frames} | 从单个实例中检索一个或多个帧。 若要指定多个帧，请使用英文逗号分隔每个帧以返回结果。 例如，/studies/1/series/2/instance/3/frames/4,5,6 |

### <a name="retrieve-instances-within-study-or-series"></a>检索检查或序列内的实例

支持以下 `Accept` 标头在检查或序列中检索实例：


* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `multipart/related; type="application/dicom";`（如未指定 transfer-syntax，则将 1.2.840.10008.1.2.1 用作默认值）
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-an-instance"></a>检索实例

支持以下 `Accept` 标头检索特定实例：

* `application/dicom; transfer-syntax=*`
* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `application/dicom;`（如未指定 transfer-syntax，则将 1.2.840.10008.1.2.1 用作默认值）
* `multipart/related; type="application/dicom"`（如未指定 transfer-syntax，则将 1.2.840.10008.1.2.1 用作默认值）
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.4.90`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-frames"></a>检索帧

支持以下 `Accept` 标头检索帧：

* `multipart/related; type="application/octet-stream"; transfer-syntax=*`
* `multipart/related; type="application/octet-stream";`（如未指定 transfer-syntax，则将 1.2.840.10008.1.2.1 用作默认值）
* `multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="image/jp2";`（如未指定 transfer-syntax，则将 1.2.840.10008.1.2.4.90 用作默认值）
* `multipart/related; type="image/jp2";transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-transfer-syntax"></a>检索传输语法

当请求的传输语法不同于原始文件时，原始文件将转码为请求的传输语法。 原始文件须为以下格式之一才能转码成功，否则，转码可能失败：

* 1.2.840.10008.1.2 (Little Endian Implicit)
* 1.2.840.10008.1.2.1 (Little Endian Explicit)
* 1.2.840.10008.1.2.2 (Explicit VR Big Endian)
* 1.2.840.10008.1.2.4.50 (JPEG Baseline Process 1)
* 1.2.840.10008.1.2.4.57 (JPEG Lossless)
* 1.2.840.10008.1.2.4.70 (JPEG Lossless Selection Value 1)
* 1.2.840.10008.1.2.4.90 (JPEG 2000 Lossless Only)
* 1.2.840.10008.1.2.4.91 (JPEG 2000)
* 1.2.840.10008.1.2.5 (RLE Lossless)

不受支持的 `transfer-syntax` 会导致 `406 Not Acceptable`。

### <a name="retrieve-metadata-for-study-series-or-instance"></a>检索（检查、序列或实例的）的元数据

支持以下 `Accept` 标头检索检查、序列或实例的元数据：

* `application/dicom+json`

检索元数据不会返回具有以下值表示形式的属性：

| VR 名称 | 说明            |
| :------ | :--------------------- |
| OB      | Other Byte             |
| OD      | Other Double           |
| OF      | Other Float            |
| OL      | Other Long             |
| OV      | Other 64-Bit Very Long |
| OW      | Other Word             |
| UN      | Unknown                |

### <a name="retrieve-metadata-cache-validation-for-study-series-or-instance"></a>检索（检查、序列或实例的）元数据缓存验证

使用 `ETag` 机制可支持缓存验证。 在对元数据请求的响应中，ETag 将作为其中一个标头返回。 此 ETag 可缓存并在以后作为 `If-None-Match` 标头添加到对相同元数据的请求中。 如果数据存在，则可能发送两种类型的响应：

* 自上次请求后数据未更改：将发送无响应正文的 HTTP 304（未修改）响应。
* 自上次请求后数据已更改：将发送具有已更新 ETag 的 HTTP 200（确定）响应。 所需数据也将作为正文的一部分返回。

### <a name="retrieve-response-status-codes"></a>检索响应状态代码

| 代码                         | 说明 |
| :--------------------------- | :---------- |
| 200 (正常)                     | 已检索所有请求的数据。 |
| 304（未修改）           | 自上次请求以来，请求的数据未修改。 在这种情况下，不会将内容添加到响应正文中。 有关详细信息，请参阅上一节：检索（检查、序列或实例的）元数据缓存验证。 |
| 400（错误的请求）            | 请求的格式不正确。 例如，提供的检查实例标识符不符合预期的 UID 格式，或者不支持所请求的 transfer-syntax 编码。 |
| 401（未授权）           | 客户端未经身份验证。 |
| 403（已禁止）              | 用户未获得授权。 |
| 404（未找到）              | 未找到指定的 DICOM 资源。 |
| 406（不可接受）         | 不支持指定的 `Accept` 标头。 |
| 503（服务不可用）    | 服务不可用或正忙。 请稍后再试。 |

## <a name="search-qido-rs"></a>搜索 (QIDO-RS)

基于 DICOM 对象 ID 的查询 (QIDO) 允许按属性搜索检查、序列和实例。

| 方法 | 路径                                            | 说明                       |
| :----- | :---------------------------------------------- | :-------------------------------- |
| 搜索检查                                                                         |
| GET    | ../studies?...                                  | 搜索检查                |
| 搜索序列                                                                          |
| GET    | ../series?...                                   | 搜索序列                 |
| GET    |../studies/{study}/series?...                    | 搜索检查中的序列      |
| 搜索实例                                                                       |
| GET    |../instances?...                                 | 搜索实例              |
| GET    |../studies/{study}/instances?...                 | 搜索检查中的实例   |
| GET    |../studies/{study}/series/{series}/instances?... | 搜索序列中的实例  |

支持以下 `Accept` 标头进行搜索：

- `application/dicom+json`

### <a name="supported-search-parameters"></a>支持的搜索参数

各查询均支持以下参数：

| 键              | 支持值（一个或多个）              | 允许的计数 | 说明 |
| :--------------- | :---------------------------- | :------------ | :---------- |
| `{attributeID}=` | {value}                       | 0...N         | 在查询中搜索属性/值匹配。 |
| `includefield=`  | `{attributeID}`<br/>`all`   | 0...N         | 响应中要返回的其他属性。 支持公有标记和私有标记。<br/>在提供 `all` 的情况下。 请参阅[搜索响应](#search-response)，了解有关每种查询类型将返回哪些属性的详细信息。<br/>如果 {attributeID} 和“all”都有提供，则服务器将默认使用“all”。 |
| `limit=`         | {value}                       | 0..1          | 整数值，用于限制响应中返回值的数量。<br/>该值的范围为 1 >= x <= 200。 默认值为 100。 |
| `offset=`        | {value}                       | 0..1          | 跳过 {value} 个结果。<br/>如果提供的偏移大于搜索查询结果的数量，则将返回 204（无内容）响应。 |
| `fuzzymatching=` | `true` \| `false`             | 0..1          | 如果模糊匹配为 true 且应用于 PatientName 属性时。 将对 PatientName 值内的任何姓名部分进行前缀字匹配。 例如，如果 PatientName 为“John^Doe”，则“joh”、“do”、“jo do”、“Doe”和“John Doe”将全部匹配。 但是，“ohn”将不匹配。 |

#### <a name="searchable-attributes"></a>可搜索属性

支持搜索以下属性和搜索类型。

| 属性关键字 | 检查 | 系列 | 实例 |
| :---------------- | :---: | :----: | :------: |
| StudyInstanceUID | X | X | X |
| PatientName | X | X | X |
| PatientID | X | X | X |
| AccessionNumber | X | X | X |
| ReferringPhysicianName | X | X | X |
| StudyDate | X | X | X |
| StudyDescription | X | X | X |
| SeriesInstanceUID |  | X | X |
| Modality |  | X | X |
| PerformedProcedureStepStartDate |  | X | X |
| SOPInstanceUID |  |  | X |

#### <a name="search-matching"></a>搜索匹配

支持以下匹配类型。

| 搜索类型 | 支持的属性 | 示例 |
| :---------- | :------------------ | :------ |
| 范围查询 | StudyDate | {attributeID}={value1}-{value2}。 对于日期/时间值，支持含标记值的范围。 会将其将映射到 `attributeID >= {value1} AND attributeID <= {value2}`。 |
| 完全匹配 | 所有支持的属性 | {attributeID}={value1} |
| 模糊匹配 | PatientName | 匹配患者姓名中以该值开头的任何部分。 |

#### <a name="attribute-id"></a>属性 ID

可通过多种方式对查询参数的标记进行编码。 我们已部分实现 [PS3.18 6.7.1.1.1](http://dicom.nema.org/medical/dicom/2019a/output/chtml/part18/sect_6.7.html#sect_6.7.1.1.1) 中定义的标准。 支持以下标记编码：

| 值            | 示例          |
| :--------------- | :--------------- |
| {group}{element} | 0020000D         |
| {dicomKeyword}   | StudyInstanceUID |

搜索实例的查询示例：../instances?Modality=CT&00280011=512&includefield=00280010&limit=5&offset=0

### <a name="search-response"></a>搜索响应

此响应将是 DICOM 数据集的一个数组。 根据不同的资源，默认情况下将返回以下属性：

#### <a name="default-study-tags"></a>默认检查标记

| 标记          | 属性名称 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0020) | StudyDate |
| (0008, 0030) | StudyTime |
| (0008, 0050) | AccessionNumber |
| (0008, 0056) | InstanceAvailability |
| (0009, 0090) | ReferringPhysicianName |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0010, 0010) | PatientName |
| (0010, 0020) | PatientID |
| (0010, 0030) | PatientBirthDate |
| (0010, 0040) | PatientSex |
| (0020, 0010) | StudyID |
| (0020，000D) | StudyInstanceUID |

#### <a name="default-series-tags"></a>默认序列标记

| 标记          | 属性名称 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0060) | Modality |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0008, 103E) | SeriesDescription |
| (0020，000E) | SeriesInstanceUID |
| (0040, 0244) | PerformedProcedureStepStartDate |
| (0040, 0245) | PerformedProcedureStepStartTime |
| (0040, 0275) | RequestAttributesSequence |

#### <a name="default-instance-tags"></a>默认实例标记

| 标记          | 属性名称 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0016) | SOPClassUID |
| (0008, 0018) | SOPInstanceUID |
| (0008, 0056) | InstanceAvailability |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0020, 0013) | InstanceNumber |
| (0028, 0010) | “行” |
| (0028, 0011) | 列 |
| (0028, 0100) | BitsAllocated |
| (0028, 0008) | NumberOfFrames |

如果 includefield=all，则除默认属性外，还将包括以下属性。 这些属性与默认属性一起构成每个资源级别受支持属性的完整列表。

#### <a name="other-study-tags"></a>其他检查标记

| 标记          | 属性名称 |
| :----------- | :------------- |
| (0008, 1030) | Study Description |
| (0008, 0063) | AnatomicRegionsInStudyCodeSequence |
| (0008, 1032) | ProcedureCodeSequence |
| (0008, 1060) | NameOfPhysiciansReadingStudy |
| (0008, 1080) | AdmittingDiagnosesDescription |
| (0008, 1110) | ReferencedStudySequence |
| (0010, 1010) | PatientAge |
| (0010, 1020) | PatientSize |
| (0010, 1030) | PatientWeight |
| (0010, 2180) | 职业 |
| (0010, 21B0) | AdditionalPatientHistory |

#### <a name="other-series-tags"></a>其他序列标记

| 标记          | 属性名称 |
| :----------- | :------------- |
| (0020, 0011) | SeriesNumber |
| (0020, 0060) | Laterality |
| (0008, 0021) | SeriesDate |
| (0008, 0031) | SeriesTime |

返回以下属性：

* 资源 url 中的所有匹配查询参数和 UID。
* 该资源级别支持的 IncludeField 属性。 
* 如果目标资源为所有序列，则还会返回检查级别的属性。
* 如果目标资源是所有实例，则还会返回检查级别和序列级别的属性。
* 如果目标资源是检查的实例，则还会返回序列级别的属性。

### <a name="search-response-codes"></a>搜索响应代码

查询 API 将在响应中返回以下状态代码之一：

| 代码                      | 说明 |
| :------------------------ | :---------- |
| 200 (正常)                  | 响应有效负载包含所有匹配的资源。 |
| 204 (无内容)          | 搜索成功完成，但未返回任何结果。 |
| 400（错误的请求）         | 由于查询的部分无效，服务器无法执行查询。 响应正文包含查询失败的详细信息。 |
| 401（未授权）        | 客户端未经身份验证。 |
| 403（已禁止）           | 用户未获得授权。 |
| 503（服务不可用） | 服务不可用或正忙。 请稍后再试。 |

### <a name="extra-notes"></a>额外说明

* 不支持使用 `TimezoneOffsetFromUTC` (`00080201`) 进行查询。
* 查询 API 不会返回 413（请求实体过大）。 如果请求的查询其响应限制超出可接受的范围，则将返回错误的请求。 在可接受范围内请求的所有内容都将进行解析。
* 当目标资源是检查/序列时，多个实例中可能存在不一致的检查/序列级元数据。 例如，两个实例可能具有不同的 patientName。 在这种情况下，将会返回最新的一个，并且只能搜索最新的数据。
* 分页结果经过优化，将首先返回匹配的最新实例。 如果添加了与查询匹配的较新数据，可能导致后续页面中出现重复记录。
* 对于 PN VR 类型，匹配不区分大小写，也不区分重音。
* 对于其他字符串 VR 类型，匹配不区分大小写，但会区分重音。

## <a name="delete"></a>删除

此事务不是官方 DICOMweb&trade; 标准的一部分。 它使用 DELETE 方法从存储中删除检查、序列和实例的表示形式。

| 方法 | 路径                                                    | 说明 |
| :----- | :------------------------------------------------------ | :---------- |
| DELETE | ../studies/{study}                                      | 删除特定检查的所有实例。 |
| DELETE | ../studies/{study}/series/{series}                      | 删除检查中特定序列的所有实例。 |
| DELETE | ../studies/{study}/series/{series}/instances/{instance} | 删除序列中的特定实例。 |

参数 `study`、`series` 和 `instance` 分别对应于 DICOM 属性 StudyInstanceUID、SeriesInstanceUID 和 SopInstanceUID。

请求的 `Accept` 标头、`Content-Type` 标头或正文内容没有限制。

> [!NOTE]
> 执行 Delete 事务后，已删除的实例不可恢复。

### <a name="response-status-codes"></a>响应状态代码

| 代码                         | 说明 |
| :--------------------------- | :---------- |
| 204 (无内容)             | 所有 SOP 实例已删除。 |
| 400（错误的请求）            | 请求的格式不正确。 |
| 401（未授权）           | 客户端未经身份验证。 |
| 403（已禁止）              | 用户未获得授权。 |
| 404（未找到）              | 未在检查中找到指定序列，或未在该序列中找到指定实例。 |
| 503（服务不可用）    | 服务不可用或正忙。 请稍后再试。 |

### <a name="delete-response-payload"></a>删除响应有效负载

响应正文将为空。 状态代码是返回的唯一有用信息。

### <a name="next-steps"></a>后续步骤

有关 DICOM 服务的信息，请参阅 

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)
