---
title: 为已启用 Azure Arc 的 Kubernetes 创建加入服务主体
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: '创建支持 Azure Arc 的加入服务主体 '
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: 610b355073473f0e492350753a523b7943666f13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121739"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 创建加入服务主体

## <a name="overview"></a>概述

可以使用具有有限权限角色分配的服务主体，将 Kubernetes 群集连接到 Azure Arc。 此功能在持续集成和持续部署 (CI/CD) 管道（如 Azure Pipelines 和 GitHub Actions）中非常有用。

演练以下步骤，了解如何使用服务主体将 Kubernetes 群集连接到 Azure Arc。

## <a name="create-a-new-service-principal"></a>创建新服务主体

使用信息性名称创建一个新的服务主体，该名称对于 Azure Active Directory 租户是唯一的。

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

将“Kubernetes 群集 - Azure Arc 加入”角色分配到新创建的服务主体。 这个具有有限权限的内置 Azure 角色只允许主体向 Azure 注册群集。 具有此分配角色的主体无法更新、删除或修改订阅中的任何其他群集或资源。

由于功能有限，客户可以轻松地将此主体重用于加入多个群集。

可以在分配角色时传入相应的 `--scope` 参数来进一步限制权限。 这允许管理员将群集注册限制到订阅或资源组范围。 各类 `--scope` 参数支持以下场景：

| 资源  | `scope` 参数| 效果 |
| ------------- | ------------- | ------------- |
| 订阅 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | 服务主体可以在该订阅下的任何资源组中注册群集。 |
| 资源组 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | 服务主体只能在资源组 `myGroup` 中注册群集。 |

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

## <a name="use-service-principal-with-the-azure-cli"></a>在 Azure CLI 中使用服务主体

使用以下命令引用新创建的服务主体：

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>后续步骤

[使用 Azure Policy](./use-azure-policy.md) 管理群集配置。
