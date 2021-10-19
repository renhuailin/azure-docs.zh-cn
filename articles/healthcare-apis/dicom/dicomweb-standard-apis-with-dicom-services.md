---
title: 将 DICOMweb 标准 Api 用于 Azure 医疗保健 Api DICOM 服务
description: 本教程介绍如何将 DICOMweb 标准 Api 与 DICOM 服务一起使用。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: aersoy
ms.openlocfilehash: b219c4d624c5823804371bba156911aaa94eeccb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631980"
---
# <a name="using-dicomwebtradestandard-apis-with-dicom-services"></a>将 DICOMweb &trade; 标准 api 用于 DICOM 服务

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本教程概述了如何将 DICOMweb &trade; 标准 api 与 DICOM 服务一起使用。

DICOM 服务支持 DICOMweb 标准的一个子集 &trade; ，其中包括以下内容：

* 存储 (STOW-RS)
* 检索 (WADO-RS)
* 搜索 (QIDO-RS)

此外，还支持以下非标准 API：

* 删除
* 更改源

若要详细了解对 DICOM Web 标准 Api 的支持，请参阅 [DICOM 一致性声明](dicom-services-conformance-statement.md) 参考文档。

## <a name="prerequisites"></a>先决条件

若要使用 DICOMweb &trade; 标准 api，必须已部署 DICOM 服务的实例。 如果尚未部署 DICOM 服务的实例，请参阅 [使用 Azure 门户部署 dicom 服务](deploy-dicom-services-in-azure.md)。

部署完成后，可以使用 Azure 门户导航到新创建的 DICOM 服务，查看详细信息，包括服务 URL。 用于访问 DICOM 服务的服务 URL 将为： ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 。 发出请求时，请确保将版本指定为 url 的一部分。 有关详细信息，请参阅 [DICOM 服务的 API 版本控制文档](api-versioning-dicom-service.md)。

## <a name="overview-of-various-methods-to-use-with-dicom-service"></a>用于 DICOM 服务的各种方法的概述

由于 DICOM 服务作为 REST API 公开，因此可以使用任何新式开发语言访问该服务。 有关使用该服务的与语言无关的信息，请参阅 [DICOM 一致性声明](dicom-services-conformance-statement.md)。

若要查看特定于语言的示例，请参阅以下示例。 可以通过多种语言查看 Postman 集合示例，包括：

* Go 
* Java 
* JavaScript 
* C# 
* PHP 
* C 
* NodeJS
* Objective-C
* OCaml
* PowerShell
* Python
* Ruby 
* 反应.

### <a name="c"></a>C#

请参阅 [Using DICOMweb™ Standard api With c #](dicomweb-standard-apis-c-sharp.md) 教程，了解如何将 c # 与 DICOM 服务一起使用。

### <a name="curl"></a>cURL

卷是一种常见的命令行工具，用于调用几乎所有操作系统都可用的 web 终结点。 [下载卷](https://curl.haxx.se/download.html) 以开始着手。

若要了解如何通过 DICOM 服务使用卷，请参阅将 [DICOMWeb™标准 api 与卷教程配合](dicomweb-standard-apis-curl.md) 使用。

### <a name="python"></a>Python

请参阅 [使用 DICOMWeb™标准 api 和 Python](dicomweb-standard-apis-python.md) 教程，了解如何将 PYTHON 与 DICOM 服务一起使用。

### <a name="postman"></a>Postman

Postman 是一个用于设计、构建和测试 REST Api 的极佳工具。 [下载 Postman](https://www.postman.com/downloads/) 开始。 可以在 [Postman 学习站点](https://learning.postman.com/)上了解如何有效地使用 Postman。

使用 Postman 和 DICOMweb 标准时，需要 &trade; 注意的一点是，Postman 只能支持使用 DICOM 标准中定义的单一部分负载上传 DICOM 文件。 这是因为，Postman 在多部分/相关 POST 请求中不支持自定义分隔符。 有关详细信息，请参阅 [多部分 POST 不适用于我 # 576](https://github.com/postmanlabs/postman-app-support/issues/576)。 因此，使用多部分请求上载 DICOM 文档的 Postman 集合中的所有示例都以 [不能工作-请参阅说明] 为前缀。 使用单个部件请求进行上传的示例包含在集合中，并以 "Store-单实例" 为前缀。

若要使用 Postman 集合，需要在本地下载集合，并通过 Postman 导入集合。 若要访问此集合，请参阅 [Postman Collection 示例](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)。

## <a name="summary"></a>摘要

本教程提供了 DICOM 服务支持的 Api 的概述。 使用以下工具开始使用这些 Api：

- [将 DICOMweb 与 C 配合使用™标准 Api#](dicomweb-standard-apis-c-sharp.md)
- [使用 DICOMWeb™标准 Api 与卷](dicomweb-standard-apis-curl.md)
- [在 Python 中使用 DICOMWeb™标准 Api](dicomweb-standard-apis-python.md)
- [结合使用 DICOM Web 标准 Api 和 Postman 示例集合](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

### <a name="next-steps"></a>后续步骤

有关详细信息，请参阅

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)
