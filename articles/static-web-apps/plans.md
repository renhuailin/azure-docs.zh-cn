---
title: Azure Static Web Apps 托管计划
description: 比较和对比不同的 Azure Static Web Apps 托管计划。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/05/2021
ms.author: cshoe
ms.openlocfilehash: a29a13e11416e533a27020a745daddf6e26ef919
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729627"
---
# <a name="azure-static-web-apps-hosting-plans"></a>Azure Static Web Apps 托管计划

Azure Static Web Apps 可通过两种不同的计划（免费和标准）获得。 请参阅[标准计划费用的定价页面](https://azure.microsoft.com/pricing/details/app-service/static/)。

## <a name="features"></a>功能

| Feature | 免费计划 <br> （对于个人项目） | 标准计划 <br> （对于生产应用） |
| --- | --- | --- |
| Web 托管 | ✔ | ✔ |
| GitHub 集成 | ✔ | ✔ |
| Azure DevOps 集成 | ✔ | ✔ |
| 全局分布式静态内容 | ✔ | ✔ |
| 免费、自动续订 SSL 证书 | ✔ | ✔ |
| 过渡环境 | 每个应用 3 个 | 每个应用 10 个 |
| 最大应用大小 | 每个应用 250 MB | 每个应用 500 MB |
| 自定义域 | 每个应用 2 个 | 每个应用 5 个 |
| 通过 Azure Functions 的 API | 托管 | 托管或<br>[创建自己的 Functions 应用](functions-bring-your-own.md) |
| 身份验证提供程序集成 | [已预配置](authentication-authorization.md)<br>（服务定义） | [自定义注册](authentication-custom.md) |
| [使用函数分配自定义角色](authentication-authorization.md?tabs=function#role-management) | - | ✔ |
| 专用终结点 | - | ✔ |
| [服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/app-service-static/v1_0/) | 无  | ✔ |

## <a name="selecting-a-plan"></a>选择计划

以下方案有助于确定标准计划是否最适合你的需求。

- 预期流量超出带宽最大值。
- 要使用的现有 Azure Functions 应用要么具有 HTTP 终结点之外的触发器和绑定，要么无法转换为托管 Functions 应用。
- 需要[自定义提供程序注册](authentication-custom.md)的安全要求。
- 站点的 Web 资产总文件大小超过了存储最大值。
- 需要正式的客户支持。
- 需要三个以上的[过渡环境](review-publish-pull-requests.md)。

有关限制的详细信息，请参阅[配额指南](quotas.md)。

## <a name="changing-plans"></a>更改计划

可以通过 Azure 门户在“免费计划”或“标准计划”之间切换。

1. 在 Azure 门户中导航到“Azure Static Web Apps 资源”。

1. 在“设置”菜单下，选择“托管计划”。

1. 选择要用于静态 Web 应用的托管计划。

1. 选择“保存”。
