---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 06/04/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 1197b15f313746c0a8542bfadeb4e0123818fca8
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575617"
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
| [订阅级别部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md)的位置 | 10 |

<sup>1</sup>可以将最多 50 个标记直接应用于一个订阅。 但是，订阅可以包含无限数量的标记，这些标记应用于订阅中的资源组和资源。 每个资源或资源组的标记数限制为 50。 仅当标记数少于或等于 80,000 时，资源管理器才返回订阅中[唯一标记名和值的列表](/rest/api/resources/tags)。 当数目超过 80,000 时，仍可通过标记找到资源。

<sup>2</sup>当数量接近限额时，将自动从历史记录中删除部署。 有关详细信息，请参阅[从部署历史记录中自动删除](../articles/azure-resource-manager/templates/deployment-history-deletions.md)。