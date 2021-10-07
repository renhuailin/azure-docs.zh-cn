---
title: include 文件
description: include 文件
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: c1f5aae79daf4cf8fb3a447eba5538212e3dc327
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533801"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>从 Azure 门户检索主机名、端口和访问密钥

若要连接到某个 Azure Cache for Redis 实例，缓存客户端需要该缓存的主机名、端口和密钥。 在某些客户端中，这些项的名称可能略有不同。 可以从 [Azure 门户](https://portal.azure.com)检索主机名、端口和访问密钥。

- 若要获取访问密钥，请在缓存的左侧导航中选择“访问密钥”。  
  
  ![Azure Redis 缓存密钥](media/redis-cache-access-keys/redis-cache-keys.png)

- 若要获取主机名和端口，请在缓存的左侧导航栏中选择“属性”。  主机名的格式为 \<DNS name>.redis.cache.windows.net。

  ![Azure Redis 缓存属性](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

