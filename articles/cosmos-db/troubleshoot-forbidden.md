---
title: 排除 Azure Cosmos DB 禁止的异常
description: 了解如何诊断和修复禁止的异常。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99971899"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>诊断并解决 Azure Cosmos DB 禁止的异常
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 状态代码403表示禁止请求完成。

## <a name="firewall-blocking-requests"></a>防火墙阻止请求
在此方案中，通常会看到如下错误：

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>解决方案
验证当前 [防火墙设置](how-to-configure-firewall.md) 是否正确，并包括要尝试连接的 ip 或网络。
如果最近更新了，请记住， **最多可能需要15分钟才能应用** 更改。

## <a name="next-steps"></a>后续步骤
* 配置 [IP 防火墙](how-to-configure-firewall.md)。
* 配置 [虚拟网络](how-to-configure-vnet-service-endpoint.md)的访问权限。
* 配置来自 [专用终结点](how-to-configure-private-endpoints.md)的访问权限。
