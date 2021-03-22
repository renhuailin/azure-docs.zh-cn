---
title: 配置 Azure Static Web Apps
description: 了解如何为 Azure Static Web Apps 配置路由、强制实施安全规则和全局设置。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: 324a8e75488d74fc6aa52e499b8dde616cd9beb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034041"
---
# <a name="configure-azure-static-web-apps"></a>配置 Azure Static Web Apps

Azure Static Web Apps 的配置在 _staticwebapp.config.json_ 文件中定义，该文件控制以下设置：

- 路由
- 身份验证
- 授权
- 回退规则
- HTTP 响应替代
- 全局 HTTP 头定义
- 自定义 MIME 类型

## <a name="file-location"></a>文件位置

建议将 _staticwebapp.config.json_ 置于[工作流文件](./github-actions-workflow.md)中设置为 `app_location` 的文件夹内。 但是，可将该文件放在应用程序源代码文件夹中的任何位置。

有关详细信息，请参阅[示例配置](#example-configuration-file)文件。

> [!IMPORTANT]
> 如果 _staticwebapp.config.json_ 存在，则会忽略 [_routes.json_ 文件](./routes.md)。

## <a name="routes"></a>路由

使用路由规则可以定义允许通过 Web 访问你的应用程序的 URL 模式。 路由定义为路由规则的数组。 有关用法示例，请参阅[示例配置文件](#example-configuration-file)。

- 规则按照其在 `routes` 数组中显示的顺序执行。
- 规则评估将在发生首次匹配后停止 - 路由规则不会链接到一起。
- 你可以全面控制自定义角色名称。
  - 有几个包含 [`anonymous`](./authentication-authorization.md) 和 [`authenticated`](./authentication-authorization.md) 的内置角色名称。

路由问题在很大程度上与身份验证（识别用户的身份）和授权（为用户赋能）的概念息息相关。 务必要阅读[身份验证和授权](authentication-authorization.md)指南以及本文。

静态内容的默认文件为 *index.html* 文件。

## <a name="defining-routes"></a>定义路由

每个规则都包含一个路由模式，以及一个或多个可选的规则属性。 有关用法示例，请参阅[示例配置文件](#example-configuration-file)。

| 规则属性  | 必选 | 默认值 | 注释                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | 是      | 不适用          | 调用方请求的路由模式。<ul><li>路由路径的末尾支持[通配符](#wildcards)。<ul><li>例如，路由 admin/\* 与 admin 路径下的任何路由匹配。</ul></ul>|
| `rewrite`        | 否       | 不适用          | 定义从请求返回的文件或路径。<ul><li>与 `redirect` 规则互斥<li>重写规则不会更改浏览器的位置。<li>值必须是相对于应用的根目录的值</ul>  |
| `redirect`        | 否       | 不适用          | 定义请求的文件或路径重定向目标。<ul><li>与 `rewrite` 规则互斥。<li>重定向规则更改浏览器的位置。<li>默认响应代码为 [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302)（暂时重定向），但可以使用 [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301)（永久重定向）进行替代。</ul> |
| `allowedRoles` | 否       | 匿名     | 定义访问路由所需的角色名称列表。 <ul><li>有效字符包括 `a-z`、`A-Z`、`0-9` 和 `_`。<li>内置角色 [`anonymous`](./authentication-authorization.md) 适用于所有未经身份验证的用户<li>内置角色 [`authenticated`](./authentication-authorization.md) 适用于任何已登录的用户。<li>用户必须至少属于一个角色。<li>角色在 OR 基础上进行匹配。<ul><li>如果用户处于列出的任何角色中，则授予访问权限。</ul><li>单个用户通过[邀请](authentication-authorization.md)关联到角色。</ul> |
| `headers`<a id="route-headers"></a> | 否 | 不适用 | 添加到响应的一组 [HTTP 头](https://developer.mozilla.org/docs/Web/HTTP/Headers)。 <ul><li>当路由特定的头与响应中的全局头相同时，路由特定的头将替代 [`globalHeaders`](#global-headers)。<li>若要删除头，请将值设置为空字符串。</ul> |
| `statusCode`   | 否       | 用于重定向的 `200`、`301` 或 `302` | 响应的 [HTTP 状态代码](https://developer.mozilla.org/docs/Web/HTTP/Status)。 |
| `methods` | 否 | 所有方法 | 与路由匹配的请求方法的列表。 可用方法包括：`GET`、`HEAD`、`POST`、`PUT`、`DELETE`、`CONNECT`、`OPTIONS`、`TRACE` 和 `PATCH`。 |

每个属性在请求/响应管道中都有特定的用途。

| 目的 | 属性 |
|---|---|
| 匹配路由 | `route`, `methods` |
| 匹配路由后授权 | `allowedRoles` |
| 匹配并授权规则后进行处理 | `rewrite`（修改请求） <br><br>`redirect`、`headers`、`statusCode`（修改响应） |

## <a name="securing-routes-with-roles"></a>使用角色保护路由

通过将一个或多个角色名称添加到规则的 `allowedRoles` 数组来保护路由，用户通过[邀请](./authentication-authorization.md)关联到自定义角色。 有关用法示例，请参阅[示例配置文件](#example-configuration-file)。

默认情况下，每个用户都属于内置 `anonymous` 角色，所有登录用户都是 `authenticated` 角色成员。

例如，若要将路由限制为仅经过身份验证的用户，请将内置 `authenticated` 角色添加到 `allowedRoles` 数组。

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

可以根据需要在 `allowedRoles` 数组中创建新角色。 若要仅限管理员进行路由，可以在 `allowedRoles` 数组中定义自己的名为 `administrator` 的角色。

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- 你可以全面控制角色名称；不存在角色必须遵循的列表。
- 单个用户通过[邀请](authentication-authorization.md)关联到角色。

## <a name="wildcards"></a>通配符

通配符规则匹配路由模式中的所有请求，仅支持在路径末尾使用，可按文件扩展名进行筛选。 有关用法示例，请参阅[示例配置文件](#example-configuration-file)。

例如，若要为日历应用程序实现路由，可以重写 calendar 路由下的所有 URL，以提供单个文件。

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

然后，calendar.html 文件可使用客户端路由为 URL 变体（如 `/calendar/january/1`、`/calendar/2020` 和 `/calendar/overview`）提供不同的视图。

可按文件扩展名筛选通配符匹配项。 例如，如果想要添加仅匹配给定路径中的 HTML 文件的规则，可以创建以下规则：

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

若要按多个文件扩展名进行筛选，请将选项括在大括号中，如以下示例中所示：

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

通配符路由的常见用例包括：

- 为整个路径模式提供一个特定文件
- 将不同的 HTTP 方法映射到整个路径模式
- 强制实施身份验证和授权规则
- 实现专用的缓存规则

## <a name="fallback-routes"></a>回退路由

单页应用程序通常依赖于客户端路由。 这些客户端路由规则无需向服务器发回请求即可更新浏览器的窗口位置。 如果刷新页面或者直接导航到客户端路由规则生成的 URL，则需要使用服务器端回退路由来提供相应的 HTML 页面（通常是客户端应用的 _index.html_）。

可将应用配置为使用实现回退路由的规则，如以下示例中所示。该示例的文件筛选器使用了路径通配符：

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

示例文件的结构如下。使用此规则可获得以下结果。

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| 请求... | 返回... | 以及状态... |
| --- | --- | --- |
| */about/* | *index.html* 文件 | `200` |
| */images/logo.png* | 图像文件  | `200` |
| */images/icon.svg* | */index.html* 文件 - 由于 *svg* 文件扩展名未在 `/images/*.{png,jpg,gif}` 筛选器中列出   | `200` |
| */images/unknown.png* | “未找到文件”错误  | `404` |
| */css/unknown.css* | “未找到文件”错误  | `404` |
| */css/global.css* | 样式表文件 | `200` |
| */images* 或 */css* 文件夹外部的任何其他文件 | *index.html* 文件 | `200` |

## <a name="global-headers"></a>全局头

`globalHeaders` 节提供一组应用于每个响应的 [HTTP 头](https://developer.mozilla.org/docs/Web/HTTP/Headers)，除非由[路由头](#route-headers)规则替代，否则将从路由和全局头返回两个头的并集。

有关用法示例，请参阅[示例配置文件](#example-configuration-file)。

若要删除头，请将值设置为空字符串 (`""`)。

全局头的一些常见用例包括：

- 自定义缓存规则
- 强制实施安全策略
- 编码设置

## <a name="response-overrides"></a>响应替代

在 `responseOverrides` 节中，可以定义当服务器返回错误代码时要做出的自定义响应。 有关用法示例，请参阅[示例配置文件](#example-configuration-file)。

可使用以下 HTTP 代码进行替代：

| 状态代码 | 含义 | 可能的原因 |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | 错误的请求 | 邀请链接无效 |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | 未授权 | 在未经身份验证的情况下请求访问受限页面 |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | 禁止 |<ul><li>用户已登录，但没有所需的角色，无法查看该页面。<li>用户已登录，但运行时无法从其标识声明中获取用户详细信息。<li>有太多的用户使用自定义角色登录到站点，因此运行时无法将该用户登录。</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | 未找到 | 找不到文件 |

以下示例配置演示如何替代错误代码。

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>示例配置文件

```json
{
    "routes": [
        {
            "route": "/profile",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/admin/*",
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404,
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    },
}
```

根据上述配置查看以下场景。

| 请求... | 结果... |
| --- | --- |
| /profile | 向经过身份验证的用户提供 /profile/index.html 文件。 未经身份验证的用户重定向到 _/login_。 |
| _/admin/_ | 为充当 _administrator_ 角色的经过身份验证的用户提供 _/admin/index.html_ 文件。 为不充当 _administrator_ 角色的经过身份验证的用户提供 `403` 错误 <sup>1</sup>。 未经身份验证的用户重定向到 _/login_。 |
| _/logo.png_ | 使用自定义缓存规则提供图像，该规则中的最大期限略微超过 182天（15,770,000 秒）。 |
| /api/admin | 将充当 _registeredusers_ 角色的经过身份验证的用户发来的 `GET` 请求发送到 API。 为不充当 _registeredusers_ 角色的经过身份验证的用户以及未经过身份验证的用户提供 `401` 错误。<br/><br/>将充当 _administrator_ 角色的经过身份验证的用户发来的 `POST`、`PUT`、`PATCH` 和 `DELETE` 请求发送到 API。 为不充当 _administrator_ 角色的经过身份验证的用户以及未经过身份验证的用户提供 `401` 错误。 |
| /customers/contoso | 为属于 _administrator_ 或 _customers\_contoso_ 角色的经过身份验证的用户提供 _/customers/contoso/index.html_ 文件。 为不充当 _administrator_ 或 _customers\_contoso_ 角色的经过身份验证的用户提供 `403` 错误<sup>1</sup>。 未经身份验证的用户重定向到 _/login_。 |
| /login | 未经身份验证的用户将面临在 GitHub 中进行身份验证的挑战。 |
| /.auth/login/twitter | 由于路由规则禁用了 Twitter 授权，因此返回了 `404` 错误，从而回退为提供 _/index.html_ 和 `200` 状态代码。 |
| /logout | 用户已注销任何身份验证提供程序。 |
| _/calendar/2021/01_ | 将向浏览器提供 /calendar.html 文件。 |
| /specials | 浏览器永久重定向到 _/deals_。 |
| _/data.json_ | 提供 `text/json` MIME 类型的文件。 |
| _/about_，或与客户端路由模式匹配的任何文件夹 | 提供 _/index.html_ 文件和 `200` 状态代码。 |
| _/images/_ 文件夹中不存在的文件 | `404` 错误。 |

<sup>1</sup> 可以使用[响应替代规则](#response-overrides)提供自定义错误页。

## <a name="restrictions"></a>限制

_staticwebapps.config.json_ 文件存在以下限制。

- 最大文件大小为 100 KB
- 最多 50 个不同角色

有关一般限制和局限性，请参阅[有关配额的文章](quotas.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序身份验证和授权](authentication-authorization.md)
