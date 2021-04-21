---
title: CLI：将应用连接到 Redis
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何将应用连接到 Azure Cache for Redis。
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 24658525ca1248aa82732a31eb9a40df1403203c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782380"
---
# <a name="connect-an-app-service-app-to-an-azure-cache-for-redis-using-cli"></a>使用 CLI 将应用服务应用连接到 Azure Redis 缓存

此示例脚本创建一个 Azure Redis 缓存和一个应用服务应用。 然后，它使用应用设置将 Azure Redis 缓存链接到应用。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Azure Redis 缓存和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az redis create`](/cli/azure/redis#az_redis-create) | 创建新的用于 Redis 的 Azure 缓存实例。 |
| [`az redis list-keys`](/cli/azure/redis#az_redis_list_keys) | 列出用于 Redis 的 Azure 缓存实例的访问密钥。 |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | 创建或更新应用服务应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
