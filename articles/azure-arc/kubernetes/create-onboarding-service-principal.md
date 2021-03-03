---
title: 为启用了 Azure Arc 的 Kubernetes 创建载入服务主体
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: '创建启用了 Azure Arc 的载入服务主体 '
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: bda088bdae5c866493718db94c9a2da89cada8c9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650340"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>为启用了 Azure Arc 的 Kubernetes 创建载入服务主体

## <a name="overview"></a>概述

可以使用具有有限权限角色分配的服务主体，将 Kubernetes 群集连接到 Azure Arc。 此功能可用于 (CI/CD) 管道中的持续集成和持续部署，如 Azure Pipelines 和 GitHub 操作。

演练以下步骤，了解如何使用服务主体将 Kubernetes 群集连接到 Azure Arc。

## <a name="create-a-new-service-principal"></a>创建新服务主体

使用信息性名称创建一个新的服务主体，该服务主体是 Azure Active Directory 租户的唯一信息。

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**输出：**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>分配权限

将 "Kubernetes Cluster-Azure Arc 载入" 角色分配到新创建的服务主体。 此具有有限权限的内置 Azure 角色仅允许主体将群集注册到 Azure。 具有此已分配角色的主体无法更新、删除或修改订阅中的任何其他群集或资源。

由于功能有限，客户可以轻松地将此主体重用于加入多个群集。

可以在分配角色时传入相应的参数，进一步限制权限 `--scope` 。 这允许管理员将群集注册限制到订阅或资源组的作用域。 各类 `--scope` 参数支持以下场景：

| 资源  | `scope` 参数| 效果 |
| ------------- | ------------- | ------------- |
| 订阅 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | 服务主体可以在该订阅下的任何资源组中注册群集。 |
| 资源组 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | 服务主体 __只能__ 注册资源组中的群集 `myGroup` 。 |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**输出：**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>将服务主体用于 Azure CLI

将新创建的服务主体引用为以下命令：

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
