---
title: CLI：从 ACR 创建 ASP.NET Core 应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示了如何从 ACR 创建 Linux ASP.NET Core 应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 037d66b4cc94fba5a0dee2a44b6a9e316dd36f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787733"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>在应用服务的 Docker 容器中通过 Azure 容器注册表创建 ASP.NET Core 应用

此示例脚本将创建一个资源组、一个 Linux 应用服务计划和一个应用。 然后，它将使用 Docker 容器从 Azure 容器注册表部署 ASP.NET Core 应用程序。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

需要 Azure CLI 2.0.52 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) | 为应用服务应用设置 Docker 容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
