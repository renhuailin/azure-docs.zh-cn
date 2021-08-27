---
title: 包含文件
description: include 文件
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/17/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 5c4a4ab57e2e4a63579cc753e183cd9cecc4b765
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867920"
---
我们提供了不同的模板来处理特定载入方案。 选择最适合的选项，并确保修改参数文件以反映环境。 要详细了解如何在部署中使用这些文件，请参阅[将客户加入 Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md)。

| **模板** | **说明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | 将客户的订阅加入 Azure Lighthouse。 必须为每个订阅执行单独的部署。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | 将客户的一个或多个资源组加入 Azure Lighthouse。 将 **rgDelegatedResourceManagement** 用于单个资源组，或使用 **multipleRgDelegatedResourceManagement**  载入同一订阅中的多个资源组。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | 如果已[向 Azure 市场发布托管服务产品](../articles/lighthouse/how-to/publish-managed-services-offers.md)，则可以选择使用此模板为已接受该产品的客户载入资源。 参数文件中的 marketplace 值必须与发布产品时使用的值匹配。 |

如果要包括[符合条件的授权](../articles/lighthouse/how-to/create-eligible-authorizations.md)（当前为公共预览版），请从[我们的示例存储库的 delegated-resource-management-eligible-authorizations 部分](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations)中选择对应的模板。

通常，每个载入的订阅都需要单独部署，但你也可以跨多个订阅部署模板。

| **模板** | **说明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | 跨多个订阅部署 Azure 资源管理器模板。 |


