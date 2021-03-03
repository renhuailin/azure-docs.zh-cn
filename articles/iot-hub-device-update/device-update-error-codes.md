---
title: Azure IoT 中心的设备更新的客户端错误代码 |Microsoft Docs
description: 本文档提供了各种设备更新组件的客户端错误代码表。
author: lichris
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: 5251d0cb09e40305d1efd89c31d3af0fa36ad385
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662186"
---
# <a name="device-update-for-iot-hub-error-codes"></a>IoT 中心的设备更新错误代码

本文档提供了各种设备更新组件的错误代码表。 这适用于想要尝试分析自己的错误代码来诊断和解决问题的用户的参考。

有两个主要的客户端组件可能会引发错误代码：设备更新代理和传递优化代理。

## <a name="device-update-agent"></a>设备更新代理

### <a name="resultcode-and-extendedresultcode"></a>ResultCode 和 ExtendedResultCode

IoT 中心核心 PnP 接口的设备更新报告 `ResultCode` 和 `ExtendedResultCode` ，可用于诊断故障。 [了解](device-update-plug-and-play.md) 有关设备更新核心 PnP 接口的详细信息。

#### <a name="resultcode"></a>ResultCode

`ResultCode` 是一般状态代码，并遵循 http 状态代码约定。
[详细了解](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) http 状态代码。

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` 是包含编码错误信息的整数。

您很可能会 `ExtendedResultCode` 在 PnP 接口中看到作为有符号整数。 若要对进行解码 `ExtendedResultCode` ，请将有符号整数转换为无符号的十六进制。 仅使用的前4个字节 `ExtendedResultCode` ，其形式为 `F` `FFFFFFF` 第一个半字节是 **设施代码** ，其余位是 **错误代码**。

**设施代码**

| 设施代码     | 说明  |
|-------------------|--------------|
| D                 | DO SDK 引发的错误|
| E                 | 错误代码是一个 errno |


例如：

`ExtendedResultCode` 为 `-536870781`

的无符号十六进制表示形式 `-536870781` 为 `FFFFFFFF E0000083` 。

| 忽略    | 设施代码  | 错误代码   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` 用十六进制表示 `131` ，这是的 errno 值 `ENOLCK` 。

## <a name="delivery-optimization-agent"></a>传递优化代理
下表列出了与 "传递优化" ("设备更新" 客户端) 组件相关的错误代码。 DO 组件负责将更新内容下载到 IoT 设备上。

可以通过检查为响应 API 调用而引发的异常来获取 DO 错误代码。

| 错误代码  | 字符串错误                       | 类型                 | 说明 |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | 不适用                  | 传递优化无法提供服务 |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | 下载作业         | 文件下载在定义的时间段内未看到任何进度 |
| 0x80D02003L | DO_E_JOB_NOT_FOUND                 | 下载作业         | 找不到作业 |
| 0x80D02005L | DO_E_NO_DOWNLOADS                  | 下载作业         | 当前不存在任何下载 |
| 0x80D0200CL | DO_E_JOB_TOO_OLD                   | 下载作业         | 作业未完成，或在达到最大期限阈值之前已取消 |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | 下载作业         | 使用未知属性 ID 调用了 SetProperty () 或 GetProperty ()  |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | 下载作业         | 无法对只读属性调用 SetProperty ()  |
| 0x80D02013L | DO_E_INVALID_STATE                 | 下载作业         | 当前作业状态中不允许所请求的操作。 作业可能已取消或已完成传输。 它现在处于只读状态。 |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | 下载作业         | 由于未指定任何下载接收器 (本地文件或流接口) ，因此无法开始下载 |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload 接口| 下载已启动，但未提供 URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | 暂时情况 | 由于网络连接中断，下载已暂停 |
| 0x80D05001L | DO_E_HTTP_BLOCKSIZE_MISMATCH       | HTTP                 | HTTP 服务器返回的响应的数据大小不等于请求的数据大小 |
| 0x80D05002L | DO_E_HTTP_CERT_VALIDATION          | HTTP                 | HTTP 服务器证书验证失败 |
| 0x80D05010L | DO_E_INVALID_RANGE                 | HTTP                 | 指定的字节范围无效 |
| 0x80D05011L | DO_E_INSUFFICIENT_RANGE_SUPPORT    | HTTP                 | 服务器不支持必要的 HTTP 协议。 传递优化 (执行) 要求服务器支持范围协议标头 |
| 0x80D05012L | DO_E_OVERLAPPING_RANGES            | HTTP                 | 字节范围的列表包含一些不受支持的重叠范围 |
## <a name="device-update-content-service"></a>设备更新内容服务
下表列出了与设备更新服务的内容服务组件相关的错误代码。 内容服务组件负责处理更新内容的导入。

| 错误代码                    | 字符串错误                                                               | 后续步骤                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | 已存在具有相同标识的更新。                              | 请确保导入的更新尚未导入到 IoT 中心的此设备更新实例。 |
| "DuplicateContentImport"      | 同时导入了相同的内容。                  | 与 UpdateAlreadyExists 相同。 |
| "CannotProcessImportManifest" | 处理导入清单时出错。                                          | 有关正确的导入清单格式设置，请参阅 [导入概念](./import-concepts.md) 和 [导入更新](./import-update.md) 文档。 |
| "CannotDownload"              | 无法下载导入清单。                                           | 检查以确保导入清单文件的 URL 仍然有效。 |
| "CannotParse"                 | 无法分析导入清单。                                              | 根据 [导入更新](./import-update.md) 文档中定义的架构检查导入清单的准确性。 |
| "UnsupportedVersion"          | 不支持导入清单架构版本。                           | 请确保导入清单使用的是 [导入更新](./import-update.md) 文档中定义的最新架构。 |
| "UpdateLimitExceeded"         | 由于超出限制，导入更新时出错。                              | 已达到 IoT 中心的设备更新实例中允许的不同提供程序、名称或版本的数量限制。 从实例中删除一些更新，然后重试。 |
| "UpdateProvider"              | 无法导入新的更新提供程序。                                       | 已达到 IoT 中心的设备更新实例中允许的不同 __提供程序__ 数量的限制。 从实例中删除一些更新，然后重试。 |
| E                  | 无法为指定的提供程序导入新的更新名称。                | 已达到 IoT 中心的设备更新实例中的一个提供程序所允许的不同 __名称__ 的数量限制。 从实例中删除一些更新，然后重试。 |
| UpdateVersion               | 无法为指定的提供程序和名称导入新的更新版本。    | 已达到 IoT 中心的设备更新实例中的一个提供程序和名称下允许的不同 __版本__ 数的限制。 从实例中删除具有该名称的一些更新，然后重试。 |
| "UpdateProviderCompatibility" | 无法导入具有指定兼容性的其他更新提供程序。 | 在导入清单中定义设备制造商和设备型号兼容性属性时，请记住，IoT 中心的设备更新支持给定制造商/型号的单个提供程序和名称组合。 这意味着，如果你尝试使用具有多个提供程序/名称组合的相同制造商/型号兼容性属性，你会看到这些错误。 若要解决此问题，请确保 (制造商/型号定义的指定设备的所有更新) 使用相同的提供程序和名称。 虽然不是必需的，但你可能需要考虑使提供程序与制造商和名称与模型相同，只是为了简单起见。 |
| "UpdateNameCompatibility"     | 无法导入具有指定兼容性的其他更新名称。     | 与 UpdateProviderCompatibility. ContentLimitNamespaceCompatibility 相同。 |
| "UpdateVersionCompatibility"  | 无法导入具有指定兼容性的其他更新版本。  | 与 UpdateProviderCompatibility. ContentLimitNamespaceCompatibility 相同。 |
| "CannotProcessUpdateFile"     | 处理源文件时出错。                                              |                                    |
| "ContentFileCannotDownload"   | 无法下载源文件。                                               | 检查以确保 () 的更新文件的 URL 仍然有效。 |

**[下一步：解决设备更新问题](.\troubleshoot-device-update.md)**