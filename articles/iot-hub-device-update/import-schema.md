---
title: 将更新导入 Device Update for IoT Hub - 架构和其他信息 | Microsoft Docs
description: 将更新导入 Device Update for IoT Hub 时使用的架构和其他相关信息（包括对象）。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 989535d0bd6f514e63c7cea9e5fd71912f8fb08b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780151"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>将更新导入 Device Update for IoT Hub - 架构和其他信息
如果要将更新导入 Device Update for IoT Hub，请确保先查看[概念](import-concepts.md)和[操作指南](import-update.md)。 如果你对构造导入清单时使用的架构的详细信息，以及相关对象的信息感兴趣，请参阅下文。

## <a name="import-manifest-schema"></a>导入清单架构

| 名称 | 类型 | 说明 | 限制 |
| --------- | --------- | --------- | --------- |
| UpdateId | （属于`UpdateId` 对象）的父级。 | 更新标识。 |
| UpdateType | 字符串 | 更新类型： <br/><br/> * 使用引用代理执行基于程序包的更新时，指定 `microsoft/apt:1`。<br/> * 使用引用代理执行基于映像的更新时，指定 `microsoft/swupdate:1`。<br/> * 使用示例代理模拟器时，指定 `microsoft/simulator:1`。<br/> * 如果开发自定义代理，则指定自定义类型。 | 格式： <br/> `{provider}/{type}:{typeVersion}`<br/><br/> 最多 32 个字符 |
| InstalledCriteria | 字符串 | 由代理解释的字符串，用于确定是否已成功应用更新：  <br/> * 指定 SWVersion 的“值”用于更新类型 `microsoft/swupdate:1`。<br/> * 指定 `{name}-{version}` 用于更新类型 `microsoft/apt:1`，其名称和版本从 APT 文件中获取。<br/> * 如果开发自定义代理，则指定自定义字符串。<br/> | 最多 64 个字符 |
| 兼容性 | `CompatibilityInfo` [对象](#compatibilityinfo-object)的数组 | 与此更新兼容的设备的兼容性信息。 | 最多 10 项 |
| CreatedDateTime | 日期/时间 | 创建更新的日期和时间。 | 带分隔符的 ISO 8601 日期和时间格式，采用 UTC |
| ManifestVersion | 字符串 | 导入清单架构版本。 指定 `2.0`，它将与 `urn:azureiot:AzureDeviceUpdateCore:1` 接口和 `urn:azureiot:AzureDeviceUpdateCore:4` 接口兼容。 | 必须是 `2.0` |
| 文件 | `File` 对象的数组 | 更新有效负载文件 | 最多 5 个文件 |

## <a name="updateid-object"></a>UpdateId 对象

| 名称 | 类型 | 说明 | 限制 |
| --------- | --------- | --------- | --------- |
| 提供程序 | 字符串 | 更新标识的提供程序部分。 | 1-64 个字符，字母数字，点和短划线。 |
| 名称 | 字符串 | 更新标识的名称部分。 | 1-64 个字符，字母数字，点和短划线。 |
| 版本 | 版本 | 更新标识的版本部分。 | 2 到 4 部分，以点分隔的版本号，介于 0 到 2147483647 之间。 将删除前导零。 |

## <a name="file-object"></a>File 对象

| 名称 | 类型 | 说明 | 限制 |
| --------- | --------- | --------- | --------- |
| 文件名 | 字符串 | 文件的名称 | 在更新中必须是唯一的 |
| SizeInBytes | Int64 | 文件大小，以字节为单位。 | 每个文件的最大大小为 800 MB，每个更新的总大小为 800 MB |
| 哈希 | （属于`Hashes` 对象）的父级。 | 包含文件哈希的 JSON 对象 |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo 对象

| 名称 | 类型 | 说明 | 限制 |
| --- | --- | --- | --- |
| DeviceManufacturer | 字符串 | 与更新兼容的设备的制造商。 | 1-64 个字符，字母数字，点和短划线。 |
| DeviceModel | 字符串 | 与更新兼容的设备的型号。 | 1-64 个字符，字母数字，点和短划线。 |

## <a name="hashes-object"></a>Hashes 对象

| 名称 | 必选 | 类型 | 说明 |
| --------- | --------- | --------- | --------- |
| Sha256 | True | 字符串 | 使用 SHA-256 算法的文件的 Base64 编码哈希。 |

## <a name="next-steps"></a>后续步骤

详细了解[导入概念](./import-concepts.md)。

如果已准备就绪，请尝试[导入操作指南](./import-update.md)，它将逐步引导你完成导入过程。
