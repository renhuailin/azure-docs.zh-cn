---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800325"
---
| 资源 | 限制 |
| --- | --- |
| [与 Azure Active Directory 租户关联](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)的订阅数 | 无限制 |
| 每个订阅的[协同管理员数](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |无限制 |
| 每个订阅的[资源组数](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure 资源管理器 API 请求大小 |4,194,304 字节 |
| 每个订阅的标记数<sup>1</sup> |50 |
| 每个订阅的唯一标记计算数<sup>1</sup> | 80,000 |
| 每个位置的[订阅级部署数](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup>可以将最多 50 个标记直接应用于一个订阅。 但是，订阅可以包含无限数量的标记，这些标记应用于订阅中的资源组和资源。 每个资源或资源组的标记数限制为 50。 仅当标记数少于或等于 80,000 时，资源管理器才返回订阅中[唯一标记名和值的列表](/rest/api/resources/tags)。 当数目超过 80,000 时，仍可通过标记找到资源。

<sup>2</sup>如果达到部署数限制 800，请从历史记录中删除不再需要的部署。 若要删除订阅级别的部署，请使用 [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 或 [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete)。
