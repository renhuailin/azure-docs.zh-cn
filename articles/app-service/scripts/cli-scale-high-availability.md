---
title: CLI：使用流量管理器缩放应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 本示例演示如何使用流量管理器在全球范围内进行缩放。
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 9c1c4ad16a491f7e868a395fa5c36d9605348cdb
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005655"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>使用 Azure CLI 缩放具有高可用性体系结构的全球应用服务应用

此示例脚本将创建一个资源组、两个应用服务计划、两个应用、一个流量管理器配置文件和两个流量管理器终结点。 完成本练习后，会获得一个高可用性体系结构，该体系结构基于最低网络延迟提供应用的全局可用性。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、流量管理器配置文件和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | 创建应用服务应用。 |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) | 创建 Azure 流量管理器配置文件。 |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) | 将终结点添加到 Azure 流量管理器配置文件。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
