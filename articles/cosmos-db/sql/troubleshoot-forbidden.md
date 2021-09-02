---
title: 排查 Azure Cosmos DB 的“已禁止”异常的问题
description: 了解如何诊断和修复“已禁止”异常。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e61bf75a2d45e89f75c1fafbb38d22afadfe3776
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113200"
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
