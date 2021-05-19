---
title: Device Update for IoT Hub 更新清单 | Microsoft Docs
description: 了解如何在更新过程中将属性从设备更新服务发送到设备
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030675"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Device Update for IoT Hub 更新清单

## <a name="overview"></a>概述

Device Update for IoT Hub 使用更新清单通过 [AzureDeviceUpdateCore.OrchestratorMetadata:4](./device-update-plug-and-play.md) 接口属性来传递操作和传递支持这些操作的元数据。
本文档介绍有关设备更新代理如何使用 `AzureDeviceUpdateCore.OrchestratorMetadata:4` 接口中的 `updateManifest` 属性的基础知识。 `AzureDeviceUpdateCore.OrchestratorMetadata:4` 接口属性会从 Device Update for IoT Hub 服务发送到设备更新代理。 `updateManifest` 是一个由设备更新代理进行分析的序列化 JSON 对象。

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

更新清单的用途是介绍更新的内容，即其标识、类型、安装的条件以及更新文件元数据。 此外，更新清单进行了加密签名，以允许设备更新代理验证其真实性。 有关如何使用更新清单安全导入内容的详细信息，请参阅[设备更新安全性](./device-update-security.md)文档。

## <a name="import-manifest-vs-update-manifest"></a>导入清单与更新清单

了解 Device Update for IoT Hub 中的“导入清单”和“更新清单”概念之间的差异至关重要。 
* [导入清单](./import-concepts.md)由创建相应更新的人员创建。 它描述将导入到 Device Update for IoT Hub 的更新的内容。 
* 更新清单由 Device Update for IoT Hub 服务使用导入清单中定义的某些属性自动生成。 它用于在更新过程中将相关信息传达给设备更新代理。 

每个清单类型都有其自己的架构和架构版本。

## <a name="update-manifest-properties"></a>更新清单属性

可在[此处](./device-update-plug-and-play.md)提供的接口定义中找到更新清单属性的概括性定义。 为了提供更深入的上下文，让我们进一步了解一下这些属性及其在系统中的使用方式。

### <a name="updateid"></a>updateId

包含 `provider`、`name` 和 `version`，表示用于确定为更新使用的兼容设备的确切 Device Update for IoT Hub 更新标识。

### <a name="updatetype"></a>updateType

表示特定类型的更新处理程序处理的更新的类型。 对于基于映像的更新，它遵循 `microsoft/swupdate:1` 形式；对于基于包的更新，它遵循 `microsoft/apt:1` 形式（参见下面的 `Update Handler Types` 部分）。

### <a name="installedcriteria"></a>installedCriteria

一个字符串，其中包含设备更新代理的更新处理程序用于确定是否已在设备上安装更新所需的信息。 `Update Handler Types` 部分为 Device Update for IoT Hub 支持的每种更新类型记录 `installedCriteria` 的格式。

### <a name="files"></a>files

告知设备更新代理要下载的文件和用于验证文件是否已正确下载的哈希。
下面更详细地介绍了 `files` 属性内容：

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

在 `updateManifest` 之外是 JSON 对象的 `fileUrls` 数组。

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

`fileUrls` 和 `files` 中的 `FILE_ID_STRING` 是相同的（例如，`files` 中的“0000”具有指向 `fileUrls` 中“0000”的 url）。

### <a name="manifestversion"></a>manifestVersion

一个表示架构版本的字符串。

## <a name="update-handler-types"></a>更新处理程序类型

|Update 方法|更新处理程序类型|更新类型|安装的条件|需要发布的文件|
|-------------|-------------------|----------|-----------------|--------------|
|基于映像|SWUpdate|“microsoft/swupdate:version”|参考映像将其版本的提示保存在 /etc/adu-version 文件中。  |包含 SWUpdate 映像的 .swu 文件|
|基于包|APT|“microsoft/apt:version”|`<name>` + "-" + `<version>`（APT 清单文件中定义的属性|包含 APT 配置和包列表的 `<APT Update Manifest>`.json|

