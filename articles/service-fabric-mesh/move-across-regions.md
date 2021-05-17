---
title: 将 Service Fabric 网格应用程序移动到另一区域
description: 可以通过将当前模板的副本部署到新 Azure 区域来移动 Service Fabric 网格资源。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: bce61a00ae1b6b451927b43dbcf19ddb615f79a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861168"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>将 Service Fabric 网格应用程序移动到另一 Azure 区域

> [!IMPORTANT]
> Azure Service Fabric 网格的预览版已停用。 不允许再通过 Service Fabric 网格 API 进行新的部署。 对现有部署的支持将会持续到 2021 年 4 月 28 日。
> 
> 有关详细信息，请参阅 [Azure Service Fabric 网格预览版停用](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)。

本文介绍如何将 Service Fabric 网格应用程序及其资源移到不同的 Azure 区域。 由于各种原因，你可能需要将资源移动到其他区域。 例如，为了响应中断，获得仅在特定区域中可用的功能或服务，满足内部策略和监管要求，或者满足容量规划要求。

 [Service Fabric 网格不支持](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh)在 Azure 区域之间直接移动资源。 但是，你也可以见解移动资源，方法是将当前 Azure 资源管理器模板的副本部署到新的目标区域，然后将入口流量和依赖项重定向到新创建的 Service Fabric 网格应用程序。

## <a name="prerequisites"></a>先决条件

* 入口控制器（例如[应用程序网关](../application-gateway/index.yml)）充当中介，用于在客户端和 Service Fabric 网格应用程序之间路由流量
* 目标 Azure 区域（`westus`、`eastus` 或 `westeurope`）中的 Service Fabric 网格（预览版）可用性

## <a name="prepare"></a>准备

1. 通过从最近的部署导出 Azure 资源管理器模板和参数，生成 Service Fabric 网格应用程序当前状态的快照。 为此，请使用 Azure 门户，按照[部署后导出模板](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)中的步骤进行操作。 也可以使用 [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) 或 [REST API](/rest/api/resources/resourcegroups/exporttemplate)。

2. 如果适用，请[导出同一资源组中的其他资源](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group)以在目标区域中重新部署。

3. 查看（并根据需要编辑）导出的模板，以确保现有属性值是你要在目标区域中使用的属性值。 新的 `location`（Azure 区域）是你在重新部署期间将提供的参数。

## <a name="move"></a>移动

1. 在目标区域中创建一个新的资源组（或使用现有资源组）。

2. 通过导出的模板，使用 Azure 门户，按照[从自定义模板部署资源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)中的步骤进行操作。 也可以使用 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 或 [REST API](../azure-resource-manager/templates/deploy-rest.md)。

3. 有关移动相关资源（例如 [Azure 存储帐户](../storage/common/storage-account-move.md)）的指南，请参阅[跨区域移动 Azure 资源](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)主题下面列出的各个服务的指南。

## <a name="verify"></a>验证

1. 部署完成后，测试应用程序终结点以验证你的应用程序的功能。

2. 你还可以使用 [Azure Service Fabric Mesh CLI](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli)，通过检查应用程序状态 ([az mesh app show](/cli/azure/mesh/app#az_mesh_app_show)) 和查看应用程序日志和 ([az mesh code-package-log](/cli/azure/mesh/code-package-log)) 命令来验证应用程序的状态。

## <a name="commit"></a>提交

确认目标区域中 Service Fabric 网格应用程序的等效功能后，配置入口控制器（例如，[应用程序网关](../application-gateway/redirect-overview.md)）以将流量重定向到新应用程序。

## <a name="clean-up-source-resources"></a>清理源资源

要完成 Service Fabric 网格应用程序的移动，请[删除源应用程序和/或父资源组](../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>后续步骤

* [跨区域移动 Azure 资源](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [跨区域移动 Azure 资源的支持](../azure-resource-manager/management/move-support-resources.md)
* [将资源移至新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [资源移动操作支持](../azure-resource-manager/management/move-support-resources.md
)
