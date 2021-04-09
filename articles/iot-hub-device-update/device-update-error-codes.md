---
title: Azure IoT 中心设备更新的客户端错误代码 | Microsoft Docs
description: 本文档提供各种设备更新组件的客户端错误代码表。
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200339"
---
# <a name="device-update-for-iot-hub-error-codes"></a>IoT 中心设备更新错误代码

本文档提供各种设备更新组件的错误代码表。 想要尝试分析自己的错误代码来诊断和排查问题的用户可以参考本文档。

有两个主要客户端组件可能会引发错误代码：设备更新代理和传递优化代理。

## <a name="device-update-agent"></a>设备更新代理

### <a name="resultcode-and-extendedresultcode"></a>ResultCode 和 ExtendedResultCode

IoT 中心核心 PnP 接口的设备更新会报告 `ResultCode` 和 `ExtendedResultCode`（可用于诊断故障）。 [详细了解](device-update-plug-and-play.md)设备更新核心 PnP 接口。

#### <a name="resultcode"></a>ResultCode

`ResultCode` 是一般状态代码，遵循 http 状态代码约定。
[详细了解](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) http 状态代码。

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` 是包含已编码错误信息的整数。

你很可能会将 `ExtendedResultCode` 视为 PnP 接口中的带符号整数。 若要解码 `ExtendedResultCode`，请将带符号整数转换为无符号十六进制数。 只使用 `ExtendedResultCode` 的前 4 个字节，其格式为 `F` `FFFFFFF`，其中，第一个半字节是 **设施代码**，其余的位是 **错误代码**。

**设施代码**

| 设施代码     | 说明  |
|-------------------|--------------|
| D                 | DO SDK 引发的错误|
| E                 | 错误代码是 errno |


例如：

`ExtendedResultCode` 是 `-536870781`

`-536870781` 的无符号十六进制表示形式为 `FFFFFFFF E0000083`。

| 忽略    | 设施代码  | 错误代码   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

十六进制的 `0x83` 是十进制的 `131`，即 `ENOLCK` 的 errno 值。

## <a name="delivery-optimization-agent"></a>传递优化代理
下表列出了与设备更新客户端的传递优化 (DO) 组件相关的错误代码。 DO 组件负责将更新内容下载到 IoT 设备。

可以通过检查对 API 调用做出的响应中引发的异常来获取 DO 错误代码。 可以根据 0x80D0 前缀识别所有 DO 错误代码。

| 错误代码  | 字符串错误                       | 类型                 | 说明 |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | 不适用                  | 传递优化无法提供服务 |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | 下载作业         | 文件下载操作在定义的时段内没有进展 |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | 下载作业         | 使用未知属性 ID 调用了 SetProperty() 或 GetProperty() |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | 下载作业         | 无法对只读属性调用 SetProperty() |
| 0x80D02013L | DO_E_INVALID_STATE                 | 下载作业         | 在当前作业状态下不允许请求的操作。 作业可能已取消，或已完成传输。 它目前处于只读状态。 |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | 下载作业         | 由于未指定下载接收器（本地文件或流接口），无法开始下载 |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload 接口| 下载已开始，但未提供 URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | 暂时情况 | 下载由于网络连接中断而暂停 |

## <a name="device-update-content-service"></a>设备更新内容服务
下表列出了与设备更新服务的内容服务组件相关的错误代码。 内容服务组件负责处理更新内容的导入。

| 错误代码                    | 字符串错误                                                               | 后续步骤                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | 已存在具有相同标识的更新。                              | 请确保正在导入的更新尚未导入到 IoT 中心的此设备更新实例。 |
| "DuplicateContentImport"      | 已多次同时导入相同的内容。                  | 与 UpdateAlreadyExists 相同。 |
| "CannotProcessImportManifest" | 处理导入清单时出错。                                          | 有关正确的导入清单格式设置，请参阅[导入概念](./import-concepts.md)和[导入更新](./import-update.md)文档。 |
| "CannotDownload"              | 无法下载导入清单。                                           | 检查并确保导入清单文件的 URL 仍然有效。 |
| "CannotParse"                 | 无法分析导入清单。                                              | 根据[导入更新](./import-update.md)文档中定义的架构检查导入清单的准确性。 |
| "UnsupportedVersion"          | 导入清单架构版本不受支持。                           | 确保导入清单使用的是[导入更新](./import-update.md)文档中定义的最新架构。 |
| "UpdateLimitExceeded"         | 由于超出限制，导入更新时出错。                              | 已达到 IoT 中心的设备更新实例中允许的不同“提供程序”、“名称”或“版本”数限制。 从实例中删除一些更新，然后重试。 |
| "UpdateProvider"              | 无法导入新的更新提供程序。                                       | 已达到 IoT 中心的设备更新实例中允许的不同“提供程序”数限制。 从实例中删除一些更新，然后重试。 |
| "UpdateName"                  | 无法为指定的提供程序导入新的更新名称。                | 已达到 IoT 中心设备更新实例中一个提供程序下允许的不同“名称”数限制。 从实例中删除一些更新，然后重试。 |
| "UpdateVersion"               | 无法为指定的提供程序和名称导入新的更新版本。    | 已达到 IoT 中心设备更新实例中一个提供程序和名称下允许的不同“版本”数限制。 从实例中删除具有该名称的一些更新，然后重试。 |
| "UpdateProviderCompatibility" | 无法导入具有指定兼容性的其他更新提供程序。 | 在导入清单中定义设备制造商和设备型号兼容性属性时，请记住，IoT 中心的设备更新支持对给定制造商/型号使用单个提供程序和名称组合。 这意味着，如果尝试在多个提供程序/名称组合中使用相同的制造商/型号兼容性属性，则会看到这些错误。 若要解决此问题，请确保给定设备的所有更新（按制造商/型号定义）使用相同的提供程序和名称。 建议考虑将提供程序设置为与制造商相同，并将名称设置为与模型相同（目的只是为了方便操作），但这并不是必需的。 |
| "UpdateNameCompatibility"     | 无法导入具有指定兼容性的其他更新名称。     | 与 UpdateProviderCompatibility.ContentLimitNamespaceCompatibility 相同。 |
| "UpdateVersionCompatibility"  | 无法导入具有指定兼容性的其他更新版本。  | 与 UpdateProviderCompatibility.ContentLimitNamespaceCompatibility 相同。 |
| "CannotProcessUpdateFile"     | 处理源文件时出错。                                              |                                    |
| "ContentFileCannotDownload"   | 无法下载源文件。                                               | 检查并确保更新文件的 URL 仍然有效。 |

**[下一步：排查设备更新问题](.\troubleshoot-device-update.md)**
