---
title: 将 Google Tag Manager 集成到开发人员门户
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中将 Google Tag Manager 插入托管的或自承载的开发人员门户。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a3436727470ceb073f5431ef9677fbc17afdda84
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601665"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>将 Google Tag Manager 集成到 API 管理开发人员门户

[Google Tag Manager](https://developers.google.com/tag-manager) 是 Google 创建的标记管理系统。 你可以使用它来管理 JavaScript 和 HTML 标记以便在网站上进行跟踪和分析。 例如，可以使用 Google Tag Manager 来集成 Google Analytics、热图或 LiveChat 等聊天机器人。

按照本文中的步骤在 Azure API 管理中将 Google Tag Manager 插入托管的或自承载的开发人员门户。

## <a name="add-google-tag-manager-to-your-portal"></a>将 Google Tag Manager 添加到门户

按照以下步骤将 Google Tag Manager 插入托管的或自承载的开发人员门户。

> [!IMPORTANT]
> 托管门户不需要执行步骤 1 和步骤 2。 如果有托管门户，请跳到步骤 4。

1. 为最新版本的开发人员门户设置[本地环境](developer-portal-self-host.md#step-1-set-up-local-environment)。

1. 安装 npm 包以添加[用于 Google Tag Manager 的 Paperbits](https://github.com/paperbits/paperbits-gtm)：

    ```sh
    npm install @paperbits/gtm --save
    ```

1. 在 `src` 文件夹的 `startup.publish.ts` 文件中导入并注册 GTM 模块：

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. 使用 Google Tag Manager 配置来扩展上一步中的站点配置：

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>后续步骤

- [自动化门户部署](automate-portal-deployments.md)
- [自行托管开发人员门户](developer-portal-self-host.md)