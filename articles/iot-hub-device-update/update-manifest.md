---
title: IoT 中心更新清单的设备更新 |Microsoft Docs
description: 了解如何在更新过程中将属性从设备更新服务发送到设备
author: andbrown
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ea438a4a8db8dce9cf2acb0b5b3ff4e250c3cf39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662096"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>IoT 中心更新清单的设备更新

## <a name="overview"></a>概述

IoT 中心的设备更新使用 _更新清单_ 来通信操作，同时还使用通过 [AzureDeviceUpdateCore. OrchestratorMetadata： 4](./device-update-plug-and-play.md)接口属性支持这些操作的元数据。
本文档介绍 `updateManifest` `AzureDeviceUpdateCore.OrchestratorMetadata:4` 设备更新代理如何使用接口中的属性的基础知识。 `AzureDeviceUpdateCore.OrchestratorMetadata:4`接口属性将从 IoT 中心服务的设备更新发送到设备更新代理。 `updateManifest`是一个序列化的 JSON 对象，由设备更新代理进行分析。

### <a name="an-example-update-manifest"></a>示例更新清单

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

更新清单的用途是介绍更新的内容，即其身份、类型、安装的条件和更新文件元数据。 此外，对更新清单进行了加密签名，以允许设备更新代理验证其真实性。 有关如何使用更新清单安全导入内容的详细信息，请参阅 [设备更新安全](./device-update-security.md) 文档。

## <a name="import-manifest-vs-update-manifest"></a>导入清单与更新清单

了解 "IoT 中心" 的设备更新中的导入清单和更新清单概念之间的差异非常重要。 
* [导入清单](./import-concepts.md)由创建相应更新的人员创建。 它描述将导入到 IoT 中心的设备更新中的更新的内容。 
* 通过 IoT 中心服务的设备更新，使用在导入清单中定义的某些属性，自动生成更新清单。 它用于在更新过程中将相关信息传达给设备更新代理。 

每个清单类型都有其自己的架构和架构版本。

## <a name="update-manifest-properties"></a>更新清单属性

可在 [此处](./device-update-plug-and-play.md)找到的接口定义中找到更新清单属性的高级定义。 为了提供更深入的上下文，让我们进一步了解一下这些属性及其在系统中的使用方式。

### <a name="updateid"></a>updateId

包含 `provider` 、 `name` 和 `version` ，它表示用于为更新确定兼容设备的 IoT 中心更新标识的确切设备更新。

### <a name="updatetype"></a>updateType

表示由特定类型的更新处理程序处理的更新的类型。 它遵循的形式 `microsoft/swupdate:1` 为基于映像的更新和 `microsoft/apt:1` 基于包的更新 (参阅 `Update Handler Types` 下面) 的部分。

### <a name="installedcriteria"></a>installedCriteria

一个字符串，其中包含设备更新代理的更新处理程序用于确定设备上是否已安装更新所需的信息。 `Update Handler Types`部分记录 `installedCriteria` IoT 中心设备更新支持的每个更新类型的格式。

### <a name="files"></a>files

告诉设备更新代理要下载的文件，以及将用于验证文件是否已正确下载的哈希。
下面更详细地介绍 `files` 属性内容：

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

之外的 `updateManifest` 是 `fileUrls` JSON 对象的数组。

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

`FILE_ID_STRING`在和中， `fileUrls` 和 `files` 都是相同的 (例如，中的 "0000" 在 `files`) 中具有 url "0000" `fileUrls` 。

### <a name="manifestversion"></a>manifestVersion

表示架构版本的字符串。

## <a name="update-handler-types"></a>更新处理程序类型

|Update 方法|更新处理程序类型|更新类型|安装的条件|需要用于发布的文件|
|-------------|-------------------|----------|-----------------|--------------|
|基于图像|SWUpdate|"microsoft/swupdate： version"|参考图像将其版本的提示保存在/etc/adu-version 文件中。  |包含 SWUpdate 映像的 swu 文件|
|基于包的|APT|"microsoft/apt： version"|`<name>` + "-" + `<version>` (APT 清单文件中定义的属性|`<APT Update Manifest>`包含 APT 配置和包列表的 json|

