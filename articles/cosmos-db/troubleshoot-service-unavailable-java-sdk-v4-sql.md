---
title: 排查使用 Java v4 SDK 时出现的“Azure Cosmos DB 服务不可用”异常
description: 了解如何诊断和修复使用 Java v4 SDK 时出现的“Azure Cosmos DB 服务不可用”异常。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340051"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>诊断和排查“Azure Cosmos DB Java v4 SDK 服务不可用”异常
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Java v4 SDK 无法连接到 Azure Cosmos DB。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含“服务不可用”异常的已知原因和解决方案。

### <a name="the-required-ports-are-being-blocked"></a>所需端口被阻止
验证所有[必需的端口](sql-sdk-connection-modes.md#service-port-ranges)是否已启用。

### <a name="client-side-transient-connectivity-issues"></a>客户端暂时性连接问题
当存在导致超时的暂时性连接问题时，可能会出现“服务不可用”异常。 通常，与此情况相关的堆栈跟踪会包含 `ServiceUnavailableException` 错误以及诊断详细信息。 例如：

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

请按照[请求超时故障排除步骤](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps)解决此问题。

### <a name="service-outage"></a>服务中断
检查 [Azure 状态](https://status.azure.com/status)，了解是否有正在发生的问题。


## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-java-sdk-v4-sql.md)使用 Azure Cosmos DB Java v4 SDK 时遇到的问题。
* 了解 [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) 的性能准则。