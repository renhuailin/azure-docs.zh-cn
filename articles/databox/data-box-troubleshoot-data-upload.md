---
title: 查看从 Azure Data Box 和 Azure Data Box Heavy 设备上传时发生的复制错误
description: 介绍查看和跟进可阻止从 Azure Data Box 和 Azure Data Box Heavy 设备上传文件的可重试错误。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/24/2021
ms.author: alkohli
ms.openlocfilehash: 471d5bff3fa3a4be092f1294f64b0bb8c3fdf9a8
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "129211681"
---
# <a name="review-copy-errors-in-uploads-from-azure-data-box-and-azure-data-box-heavy-devices"></a>查看从 Azure Data Box 和 Azure Data Box Heavy 设备上传时发生的复制错误

本文介绍对不可重试错误的查看和跟进，这些错误有时可阻止从 Azure Data Box 和 Azure Data Box Heavy 设备上传文件到云端。

> [!NOTE]
> 本文中的信息仅适用于导入订单。

## <a name="upload-errors-notification"></a>上传错误通知

将数据从设备上传到 Azure 时，某些文件上传有时可能会失败，因为配置错误无法通过重试解决。 在这种情况下，你会收到一条通知，让你有机会查看和修复错误，以供之后上传。

你将在 Microsoft Azure 门户中看到以下通知。 错误列在数据复制日志中，你可使用 **DATA COPY PATH 打开该日志**。 有关解决错误的指南，请参阅[不可重试上传错误的摘要](#summary-of-non-retryable-upload-errors)。

![上传期间的错误通知](media/data-box-troubleshoot-data-upload/copy-completed-with-errors-notification-01.png)

无法修复这些错误。 上传已完成但出错。 在尝试通过网络传输或新的导入订单操作再次上传之前，通知会告诉你需要解决的任何配置问题。

当你查看错误并确认准备好继续时，系统将从设备中安全地清除数据。 如果未响应通知，订单将在 14 天后自动完成。 有关分步说明，请参阅[查看错误并继续](#review-errors-and-proceed)。


## <a name="review-errors-and-proceed"></a>检查错误并继续

订单将在 14 天后自动完成。 根据通知采取措施可以加快事务的处理速度。

[!INCLUDE [data-box-review-nonretryable-errors](../../includes/data-box-review-nonretryable-errors.md)]


## <a name="summary-of-non-retryable-upload-errors"></a>不可重试上传错误摘要

以下不可重试的错误会导致通知：

|错误类别                    |错误代码 |错误消息                                                                             |
|----------------------------------|-----------|------------------------------------------------------------------------------------------|
|UploadErrorCloudHttp              |400        |错误请求（文件名无效）[了解详细信息](#bad-request-file-name-not-valid)。|
|UploadErrorCloudHttp              |400        |其中一个 HTTP 标头的值的格式不正确。 [了解详细信息](#the-value-for-one-of-the-http-headers-is-not-in-the-correct-format)。|
|UploadErrorCloudHttp              |409        |不允许此操作，因为 Blob 因策略而不可变。 [了解详细信息](#this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy)。|
|UploadErrorCloudHttp              |409        |共享的总预配容量不能超过帐户最大大小限制。 [了解详细信息](#the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit)。|
|UploadErrorCloudHttp              |409        |Blob 类型对此操作无效。 [了解详细信息](#the-blob-type-is-invalid-for-this-operation)。|
|UploadErrorCloudHttp              |409        |Blob 中当前包含租约，但请求中未指定任何租约 ID。 [了解详细信息](#there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request)。|
|UploadErrorManagedConversionError |409        |要导入的 Blob 的大小无效。 Blob 大小为 `<blob-size>` 字节。 支持的大小介于 20971520 字节和 8192 GiB 之间。 [了解详细信息](#the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib)|
<!--Temporarily removed from table: Bad Request (file property failure for Azure Files)-->

有关数据复制日志内容的详细信息，请参阅 [Azure Data Box 和 Azure Data Box Heavy 导入订单的跟踪与事件日志记录](data-box-logs.md)。

数据上传期间可能会发生的其他 REST API 错误。 有关详细信息，请参阅[常见 REST API 错误代码](/rest/api/storageservices/common-rest-api-error-codes)。

> [!NOTE]
> 错误说明中的 **后续** 部分介绍了如何在提交新导入订单或执行网络传输之前更新数据配置。 无法在当前上传中修复这些错误。


### <a name="bad-request-file-name-not-valid"></a>错误请求（文件名无效）

**错误类别：** UploadErrorCloudHttp 

**错误代码：** 400

**错误说明：** 系统可在 **准备交付** 阶段捕获大多数文件命名问题，或在上传过程中自动修复（导致出现 **有警告的复制** 状态）。 未捕获无效文件名时，文件无法上传到 Azure。

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新订单之前，请重命名所列文件，以满足 Azure 文件存储命名要求。 有关命名要求，请参阅[目录和文件名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。


<!--TEMPORARILY REMOVED. Product team may restore later. ### Bad Request (File property failure for Azure Files)

**Error category:** UploadErrorCloudHttp 

**Error code:** 400

**Error description:** Data import will fail if the upload of file properties fails for Azure Files.  

**Follow-up:** You can't fix this error in the current upload. The upload will complete with errors. Before you do a network transfer or start a new import order, *GET TROUBLESHOOTING*.-->


### <a name="the-value-for-one-of-the-http-headers-is-not-in-the-correct-format"></a>其中一个 HTTP 标头的值格式不正确。

**错误类别：** UploadErrorCloudHttp 

**错误代码：** 400

**错误说明：** 无法上传列出的 Blob，因为它们不满足 Azure 存储中 Blob 的格式或大小要求。

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新的导入订单之前，请确保：

- 列出的页 blob 与 512 字节页面边界对齐。

- 列出的块 blob 不超过 4.75 TiB 最大大小。


### <a name="this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy"></a>不允许此操作，因为 Blob 因策略而不可变

**错误类别：** UploadErrorCloudHttp 

**错误代码：** 409

**错误说明：** 如果 Blob 存储容器配置为“一次写入多次读取 (WORM)”，则上传容器中已存储的任何 Blob 都将失败。

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新导入订单之前，请确保列出的 Blob 不属于不可变的存储容器。 有关详细信息，请参阅[使用不可变的存储来存储业务关键型 Blob 数据](../storage/blobs/immutable-storage-overview.md)。


### <a name="the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit"></a>共享的总预配容量不能超过帐户最大大小限制

**错误类别：** UploadErrorCloudHttp 

**错误代码：** 409

**错误说明：** 上传失败，因为数据总大小超过了存储帐户大小限制。 例如，FileStorage 帐户的最大容量为 100 TiB。 如果总数据大小超过 100 TiB，则上传失败。  

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新导入订单之前，请确保存储帐户中所有共享总容量不会超过存储帐户的大小限制。 有关详细信息，请参阅 [Azure 存储帐户大小限制](data-box-limits.md#azure-storage-account-size-limits)。


### <a name="the-blob-type-is-invalid-for-this-operation"></a>Blob 类型对此操作无效

**错误类别：** UploadErrorCloudHttp 

**错误代码：** 409

**错误说明：** 如果正在修改目标 Blob 的数据或属性，则向云中的 Blob 导入数据将失败。

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新导入订单之前，请确保上传期间未对列出的 Blob 及其属性进行并发修改。

### <a name="there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request"></a>Blob 中当前包含租约，但请求中未指定任何租约 ID

**错误类别：** UploadErrorCloudHttp 

**错误代码：** 409

**错误说明：** 如果目标 Blob 具有活动租约，则向云中的 Blob 导入数据将失败。

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新导入订单之前，请确保列出的 Blob 没有活动租约。 有关详细信息，请参阅 Blob [的悲观并发](../storage/blobs/concurrency-manage.md?tabs=dotnet#pessimistic-concurrency-for-blobs)。


### <a name="the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib"></a>要导入的 Blob 的大小无效。 Blob 大小为 `<blob-size>` 字节。 支持的大小介于 20971520 字节和 8192 GiB 之间。

**错误类别：** UploadErrorManagedConversionError

**错误代码：** 409

**错误说明：** 列出的页 Blob 无法上传，因为大小缘故无法转换为托管磁盘。 要转换为托管磁盘，页 blob 的大小必须为 20 MB（20,971,520 字节）到 8192 GiB。

**跟进：** 无法在当前上传中修复此错误。 上传已完成但出错。 执行网络传输或启动新导入订单之前，请确保列出的每个 blob 的大小为 20 MB 到 8192 GiB。


## <a name="next-steps"></a>后续步骤

- [查看常见 REST API 错误](/rest/api/storageservices/common-rest-api-error-codes)。
- [验证 Azure 中的数据上传](data-box-deploy-picked-up.md?tabs=in-us-canada-europe#verify-data-upload-to-azure-8)