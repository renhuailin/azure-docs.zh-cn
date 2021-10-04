---
title: Azure Static Web Apps 的配置概述
description: 了解 Azure Static Web Apps 的不同配置方式。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 34a93dc1203fb404d0d6c2edfd70954545e26b15
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154919"
---
# <a name="configuration-overview"></a>配置概述

以下各个概念适用于配置静态 Web 应用。

- [应用程序配置](./configuration.md)：在 `staticwebapp.config.json` 文件中定义规则以控制应用程序的行为和功能。 使用此文件来定义路由和安全规则、自定义标头和网络设置。

- [生成配置](./build-configuration.md)：定义控制生成过程的设置。

- [应用程序设置](./application-settings.md)：设置可供后端 API 使用的应用程序级设置和环境变量。

## <a name="example-scenarios"></a>示例方案

| 若希望... | 然后... |
|---|---|
| 定义路由规则 | [在 staticwebapps.config.json 文件中创建规则](./configuration.md) |
| 对哪个分支触发生成进行设置 | [更新生成配置文件中的受跟踪分支名称](./build-configuration.md)  |
| 定义哪些安全角色有权访问路由 | [在 staticwebapps.config.json 文件中使用角色保护路由](./configuration.md#securing-routes-with-roles) |
| 设置当路由与实际文件不匹配时提供哪个 HTML 文件 | [在 staticwebapps.config.json 文件中定义回退路由](./configuration.md#fallback-routes) |
| 设置 HTTP 请求的全局标头 | [在 staticwebapps.config.json 文件中定义全局标头](./configuration.md#global-headers)|
| 定义自定义生成命令 | [在应用程序配置文件中设置自定义生成命令值](./build-configuration.md) |
| 为前端生成设置环境变量 | [在生成配置文件中定义环境变量](./build-configuration.md#environment-variables) |
| 设置 API 的环境变量 | [在门户中设置应用程序设置](./application-settings.md) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序配置](configuration.md)
