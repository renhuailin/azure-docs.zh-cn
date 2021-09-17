---
title: include 文件
description: include 文件
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/13/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: b55711662daa7d3e476cd8a3cd3c438c7893c424
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178156"
---
这些示例演示如何对已加入 Azure Lighthouse 的订阅使用 Azure Policy。

| **模板** | **说明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 分配一个策略，该策略为委派的订阅添加或删除标记（使用 modify 效果）。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | 分配将 Azure Lighthouse 委派限制为特定管理租户的策略。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 分配一个将用于审核委派分配的策略。 |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | 分配一个策略，用于确认[已将管理组中的订阅委托给管理租户](../articles/lighthouse/how-to/onboard-management-group.md)，如果未委托，则创建分配。
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | 分配一个策略，该策略对委托订阅中的 Azure Key Vault 资源启用诊断（使用 deployIfNotExists 效果）。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 分配几个策略，以便对委派的订阅启用诊断，并将所有 Windows VM 和 Linux VM 连接到按策略创建的 Log Analytics 工作区。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 将计划（多个相关的策略定义）应用于委派的订阅。 |

