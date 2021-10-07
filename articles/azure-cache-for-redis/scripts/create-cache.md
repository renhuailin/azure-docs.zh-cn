---
title: 创建 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 代码示例演示如何使用命令 az redis create 创建 Azure Cache for Redis 实例。
author: curib
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: cauribeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: c0fb70c847929ef404bdb3c2f79ea4e618b74711
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534279"
---
# <a name="create-an-azure-cache-for-redis"></a>创建 Azure Redis 缓存

本方案介绍如何创建 Azure Redis 缓存。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../includes/redis-cli-script-clean-up.md)]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组和 Azure Redis 缓存。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az redis create](/cli/azure/redis) | 创建 Azure Redis 缓存实例。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。