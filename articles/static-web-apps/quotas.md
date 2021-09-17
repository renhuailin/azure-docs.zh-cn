---
title: Azure Static Web Apps 中的配额
description: 了解与 Azure Static Web Apps 关联的配额
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5030b0fb4e714be221e28e590c463186ec81ad04
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515581"
---
# <a name="quotas-in-azure-static-web-apps"></a>Azure Static Web Apps 中的配额

Azure Static Web Apps 存在以下配额。

| Feature                     | 免费计划        | 标准计划 |
|-----------------------------|------------------|---------------|
| 包含的带宽          | 每个订阅每月 100 GB | 每个订阅每月 100 GB |
| 超额带宽           | 不可用      | 每 GB 0.20 美元 |
| 应用/Azure 订阅 | 10               | 无限制 |
| 应用大小                    | 250 MB           | 500 MB |
| 计划大小                   | 单个部署的最大应用大小为 500 MB，所有过渡和生产环境的最大应用大小为 0.50 GB  | 单个部署的最大应用大小为 500 MB，所有过渡和生产环境的最大合并应用大小为 2.00 GB |
| 预生产环境 | 3                | 10 |
| 自定义域              | 每个应用 2 个        | 每个应用 5 个 |
| 授权（使用自定义角色和路由规则） | 最多可有 25 个最终用户属于自定义角色 | 最多可有 25 个最终用户属于自定义角色 |

## <a name="github-storage"></a>GitHub 存储

GitHub Actions 和 Packages 使用 GitHub 存储，该存储具有自己的一组配额。 创建 Azure 静态 Web 应用站点时，GitHub 会在部署后存储站点的项目。

有关更多详细信息，请参阅以下资源：

- [管理 Actions 存储空间](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [关于 GitHub Actions 的计费](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [管理 GitHub Actions 的支出限制](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>后续步骤

- [概述](overview.md)
