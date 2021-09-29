---
title: 将 Application Insights 集成到开发人员门户
titleSuffix: Azure API Management
description: 了解如何将 Application Insights 集成到自承载的开发人员门户。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: b495a6141e064df80e57c4c96eff67fc928d58c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601683"
---
# <a name="integrate-application-insights-to-developer-portal"></a>将 Application Insights 集成到开发人员门户

Application Insights 是 Azure Monitor 的常用功能。 它是面向开发人员和 DevOps 专家的可扩展应用程序性能管理 (APM) 服务。 使用它来监视开发人员门户并检测性能异常。 Application Insights 包含功能强大的分析工具，可帮助你了解用户在访问开发人员门户时实际执行的操作。

## <a name="add-application-insights-to-your-portal"></a>将 Application Insights 添加到门户

按照以下步骤将 Application Insights 插入托管的或自承载的开发人员门户。

> [!IMPORTANT]
> 托管门户不需要执行步骤 1 和步骤 2。 如果有托管门户，请跳到步骤 4。

1. 为最新版本的开发人员门户设置[本地环境](developer-portal-self-host.md#step-1-set-up-local-environment)。

1. 安装 npm 包以添加[用于 Azure 的 Paperbits](https://github.com/paperbits/paperbits-azure)：

    ```console
    npm install @paperbits/azure --save
    ```

1. 在 `src` 文件夹的 `startup.publish.ts` 文件中导入并注册 Application Insights 模块：

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. 检索门户的配置：

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. 使用 Application Insights 配置扩展上一步中的站点配置：

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
- [自动化门户部署](automate-portal-deployments.md)
- [自行托管开发人员门户](developer-portal-self-host.md)
