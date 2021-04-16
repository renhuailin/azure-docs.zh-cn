---
title: Azure 静态 Web 应用预览中的配额
description: 了解与 Azure 静态 Web 应用预览关联的配额
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095163"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Azure 静态 Web 应用预览中的配额

Azure 静态 Web 应用预览存在以下配额。

> [!IMPORTANT]
> Azure 静态 Web 应用处于公开预览状态，不适用于生产。

| Feature                     | 免费计划        |
|-----------------------------|------------------|
| 包含的带宽          | 100 GB/月 |
| 超额带宽           | 不可用      |
| 应用/Azure 订阅 | 10               |
| 应用大小                    | 250 MB           |
| 预生产环境 | 3                |
| 自定义域              | 1                |
| 授权（使用自定义角色和路由规则） | 最多可有 25 个最终用户属于自定义角色 |
| Azure Functions             | 可用        |
| SLA                         | 无             |

## <a name="github-storage"></a>GitHub 存储

GitHub Actions 和 Packages 使用 GitHub 存储，该存储具有自己的一组配额。 创建 Azure 静态 Web 应用站点时，GitHub 会在部署后存储站点的项目。

有关更多详细信息，请参阅以下资源：

- [管理 Actions 存储空间](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [关于 GitHub Actions 的计费](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [管理 GitHub Actions 的支出限制](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>后续步骤

- [概述](overview.md)
