---
title: 将更新导入到 IoT 中心的设备更新-架构和其他信息 |Microsoft Docs
description: 架构和其他相关信息 (包括将更新导入到 IoT 中心的设备更新中时使用的对象) 。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054519"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>将更新导入到 IoT 中心的设备更新-架构和其他信息
如果要将更新导入到 IoT 中心的设备更新，请确保已查看 [概念](import-concepts.md) 和操作 [方法指南](import-update.md) 。 如果对构造导入清单时使用的架构的详细信息感兴趣，以及相关对象的相关信息，请参阅下文。

## <a name="import-manifest-schema"></a>导入清单架构

| 名称 | 类型 | 说明 | 限制 |
| --------- | --------- | --------- | --------- |
| UpdateId | （属于`UpdateId` 对象）的父级。 | 更新标识。 |
| UpdateType | 字符串 | 更新类型： <br/><br/> * `microsoft/apt:1` 在使用引用代理执行基于包的更新时指定。<br/> * `microsoft/swupdate:1` 在使用引用代理执行基于映像的更新时指定。<br/> * `microsoft/simulator:1` 在使用示例代理模拟器时指定。<br/> * 如果开发自定义代理，请指定自定义类型。 | 格式： <br/> `{provider}/{type}:{typeVersion}`<br/><br/> 最大值为32个字符 |
| InstalledCriteria | 字符串 | 由代理解释的字符串，用于确定是否已成功应用更新：  <br/> * 指定更新类型的 SWVersion **值** `microsoft/swupdate:1` 。<br/> * 指定 `{name}-{version}` 更新类型 `microsoft/apt:1` ，其中的名称和版本是从 APT 文件中获取的。<br/> * 指定更新类型的更新文件的哈希 `microsoft/simulator:1` 。<br/> * 如果开发自定义代理，请指定自定义字符串。<br/> | 最多 64 个字符 |
| 兼容性 | 对象数组 `CompatibilityInfo` | 与此更新兼容的设备的兼容性信息。 | 最多10个项目 |
| CreatedDateTime | 日期/时间 | 创建更新的日期和时间。 | 用 UTC 分隔的 ISO 8601 日期和时间格式 |
| ManifestVersion | 字符串 | 导入清单架构版本。 指定 `2.0` ，它将与 `urn:azureiot:AzureDeviceUpdateCore:1` 接口和接口兼容 `urn:azureiot:AzureDeviceUpdateCore:4` 。 | 必须是 `2.0` |
| 文件 | 对象数组 `File` | 更新有效负载文件 | 最多5个文件 |

## <a name="updateid-object"></a>UpdateId 对象

| 名称 | 类型 | 说明 | 限制 |
| --------- | --------- | --------- | --------- |
| 提供程序 | 字符串 | 更新标识的提供程序部分。 | 1-64 个字符，字母数字，点和短划线。 |
| 名称 | 字符串 | 命名部分更新标识。 | 1-64 个字符，字母数字，点和短划线。 |
| 版本 | version | 更新标识的版本部分。 | 2到4部分，以句点分隔的版本号，介于0到2147483647之间。 将删除前导零。 |

## <a name="file-object"></a>File 对象

| 名称 | 类型 | 说明 | 限制 |
| --------- | --------- | --------- | --------- |
| 文件名 | 字符串 | 文件的名称 | 在更新中必须是唯一的 |
| SizeInBytes | Int64 | 文件大小（以字节为单位）。 | 每个文件的最大大小为 800 MB，每个更新的总大小为 800 MB |
| 哈希 | （属于`Hashes` 对象）的父级。 | 包含文件的哈希 (es) 的 JSON 对象 |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo 对象

| 名称 | 类型 | 说明 | 限制 |
| --- | --- | --- | --- |
| DeviceManufacturer | 字符串 | 与更新兼容的设备制造商。 | 1-64 个字符，字母数字，点和短划线。 |
| DeviceModel | 字符串 | 与更新兼容的设备的型号。 | 1-64 个字符，字母数字，点和短划线。 |

## <a name="hashes-object"></a>哈希对象

| 名称 | 必选 | 类型 | 说明 |
| --------- | --------- | --------- | --------- |
| Sha256 | True | 字符串 | 使用 256 SHA-1 算法的文件的 Base64 编码哈希。 |

## <a name="next-steps"></a>后续步骤

了解有关 [导入概念](./import-concepts.md)的详细信息。

如果已准备就绪，请尝试 [导入 How-To 指南](./import-update.md)，这将逐步引导你完成导入过程。
