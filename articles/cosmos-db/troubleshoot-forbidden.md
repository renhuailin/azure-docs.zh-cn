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
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99971899"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>诊断并排查 Azure Cosmos DB 的“已禁止”异常的问题
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
验证当前[防火墙设置](how-to-configure-firewall.md)是否正确，并包括要尝试发起连接的 IP 或网络。
如果最近更新过这些设置，请记住，应用这些更改最多可能会需要 15 分钟。

## <a name="next-steps"></a>后续步骤
* 配置 [IP 防火墙](how-to-configure-firewall.md)。
* 配置从[虚拟网络](how-to-configure-vnet-service-endpoint.md)进行的访问。
* 配置从[专用终结点](how-to-configure-private-endpoints.md)进行的访问。
