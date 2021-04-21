---
title: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成 | Microsoft Docs
description: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: d30cc27fc3c546619e85bb9aabd0b31c10102e96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787800"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>使用 CLI 将应用服务与应用程序网关集成

此示例脚本将创建一个 Azure 应用服务 Web 应用、Azure 虚拟网络和应用程序网关。 然后，它将 Web 应用的流量限制为只能源自应用程序网关子网。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0.74 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Cosmos DB 和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az network vnet create`](/cli/azure/network/vnet#az_network_vnet_create) | 创建虚拟网络。 |
| [`az network public-ip create`](/cli/azure/network/public-ip#az_network_public_ip_create) | 创建公共 IP 地址。 |
| [`az network public-ip show`](/cli/azure/network/public-ip#az_network_public_ip_show) | 显示公共 IP 地址的详细信息。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | 创建应用服务 Web 应用。 |
| [`az webapp show`](/cli/azure/webapp#az_webapp_show) | 显示应用服务 Web 应用的详细信息。 |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | 将访问限制添加到应用服务 Web 应用。 |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az_network_application_gateway_create) | 创建应用程序网关。 |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az_network-application-gateway-http_settings_update) | 更新应用程序网关 HTTP 设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
