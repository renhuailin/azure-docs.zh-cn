---
title: 排查 Azure Cosmos DB 未经授权异常
description: 了解如何诊断和修复未经授权异常。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: abd128966eb560b23e2c6038fb22d4dbd04eb3a7
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664532"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>诊断和排查 Azure Cosmos DB 未经授权异常
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401：HTTP 请求中的 MAC 签名与计算出的签名不同。
如果收到 401 错误消息“HTTP 请求中的 MAC 签名与计算出的签名不同”，这可能是由以下情况所致。

对于较旧的 SDK，异常可能显示为无效的 JSON 异常，而不是正确的 401 未经授权异常。 较新的 SDK 可以正确处理此情况，并提供有效的错误消息。

## <a name="troubleshooting-steps"></a>疑难解答步骤
以下列表包含未经授权异常的已知原因和解决方案。

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>密钥未正确轮换是最常见的情况
密钥轮换后不久就会出现 401 MAC 签名问题，但无需进行任何更改，它最终会停止。 

#### <a name="solution"></a>解决方案：
密钥已轮换，且没有遵循[最佳做法](secure-access-to-data.md#key-rotation)。 完成 Azure Cosmos DB 帐户密钥轮换耗时几秒到几天不等，具体取决于 Azure Cosmos DB 帐户大小。

### <a name="the-key-is-misconfigured"></a>密钥配置错误 
401 MAC 签名问题持续出现并在使用该密钥的所有调用中发生。

#### <a name="solution"></a>解决方案：
密钥在应用程序上配置错误，且帐户使用了错误密钥或未复制整个密钥。

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>应用程序使用只读密钥进行写入操作
401 MAC 签名问题仅发生在创建或替换等写入操作中，但读取请求会成功。

#### <a name="solution"></a>解决方案：
切换应用程序以使用读/写密钥，从而允许操作成功完成。

### <a name="race-condition-with-create-container"></a>创建容器时出现争用情况
创建容器后不久会看到 401 MAC 签名问题。 此问题仅会在容器创建完成前发生。

#### <a name="solution"></a>解决方案：
创建容器时出现争用状况。 在容器创建完成之前，某个应用程序实例正在尝试访问容器。 出现此争用条件的最常见情况是，应用程序正在运行就删除了容器，并重新创建了同名的容器。 SDK 尝试使用新容器，但由于容器创建仍在进行，因此无法获得密钥。

### <a name="bulk-mode-enabled"></a>启用批量模式 
使用[启用批量模式](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/)时，读取和写入操作进行了优化，以获得最佳网络性能，并通过专用的批量 API 发送到后端。 启用批量模式执行读取操作时出现 401 错误，这通常表示应用程序正在使用[只读密钥](secure-access-to-data.md#primary-keys)。

#### <a name="solution"></a>解决方案
启用批量模式执行操作时，请使用读/写密钥或具有写入访问权限的授权机制。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
* [诊断和排查](troubleshoot-java-sdk-v4-sql.md)使用 Azure Cosmos DB Java v4 SDK 时遇到的问题。
* 了解 [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) 的性能准则。