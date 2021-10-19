---
title: 排查 Azure Cosmos DB 的“已禁止”异常的问题
description: 了解如何诊断和修复“已禁止”异常。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dcef0eccc6ca314c51b436025fb10c25dbbe04fa
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658227"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>诊断并排查 Azure Cosmos DB 的“已禁止”异常的问题
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 状态代码 403 表示已禁止完成请求。

## <a name="firewall-blocking-requests"></a>防火墙阻止请求
在这种情况下，通常会看到如下错误：

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>解决方案
验证当前[防火墙设置](../how-to-configure-firewall.md)是否正确，并包括要尝试发起连接的 IP 或网络。
如果最近更新过这些设置，请记住，应用这些更改最多可能会需要 15 分钟。

## <a name="partition-key-exceeding-storage"></a>分区键超出存储
在这种情况下，通常会看到如下错误：

```
Response status code does not indicate success: Forbidden (403); Substatus: 1014
```

```
Partition key reached maximum size of {...} GB
```

### <a name="solution"></a>解决方案
此错误表示当前[分区设计](../partitioning-overview.md#logical-partitions)和工作负载尝试存储超过给定分区键值的允许数据量。 容器中的逻辑分区数没有限制，但每个逻辑分区可以存储的数据大小有限。 可以联系支持人员了解具体情况。

## <a name="non-data-operations-are-not-allowed"></a>不允许非数据操作
当帐户中不允许进行非数据[操作](../how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations)时，就会发生这种情况。 在这种情况下，通常会看到如下错误：

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>解决方案
通过 Azure 资源管理器、Azure 门户、Azure CLI 或 Azure PowerShell 执行该操作。 或者允许执行非数据操作。

## <a name="next-steps"></a>后续步骤
* 配置 [IP 防火墙](../how-to-configure-firewall.md)。
* 配置从[虚拟网络](../how-to-configure-vnet-service-endpoint.md)进行的访问。
* 配置从[专用终结点](../how-to-configure-private-endpoints.md)进行的访问。
