---
title: 使用 Azure Static Web Apps 配置前端框架
description: Azure Static Web Apps 所需的常用前端框架的设置
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: be5415658de782c362eca0f40a17a4578932aafa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777215"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps"></a>使用 Azure Static Web Apps 配置前端框架和库

Azure Static Web Apps 要求在前端框架或库的[生成配置文件](build-configuration.md)中具有相应的配置值。

## <a name="configuration"></a>配置

下表列出了一系列框架和库的设置<sup>1</sup>。

表中各列的作用通过以下项进行说明：

- **输出位置**：列出 `output_location` 的值，即 [应用程序文件的生成版本所在的文件夹](build-configuration.md)。

- **自定义生成命令**：当框架需要不同于 `npm run build` 或 `npm run azure:build` 的命令时，可以定义 [自定义生成命令](build-configuration.md#custom-build-commands)。

| 框架 | 应用项目位置 | 自定义生成命令 |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Angular Universal](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | 不适用 |
| [Backbone.js](https://backbonejs.org/) | `/` | 不适用 |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | 不适用 |
| [Ember](https://emberjs.com/) | `dist` | 不适用 |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | 不适用 |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | 不适用 |
| [Hyperapp](https://hyperapp.dev/) | `/` | 不适用 |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | 不适用 |
| [jQuery](https://jquery.com/) | `/` | 不适用 |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | 不适用 |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | 不适用 |
| [Marko](https://markojs.com/) | `public` | 不适用 |
| [流星](https://www.meteor.com/) | `bundle` | 不适用 |
| [Mithril](https://mithril.js.org/) | `dist` | 不适用 |
| [Polymer](https://www.polymer-project.org/) | `build/default` | 不适用 |
| [Preact](https://preactjs.com/) | `build` | 不适用 |
| [React](https://reactjs.org/) | `build` | 不适用 |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [模具](https://stenciljs.com/) | `www` | 不适用 |
| [Svelte](https://svelte.dev/) | `public` | 不适用 |
| [Three.js](https://threejs.org/) | `/` | 不适用 |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | 不适用 |
| [Vue.js](https://vuejs.org/) | `dist` | 不适用 |

<sup>1</sup>上表并未详尽列出与 Azure Static Web Apps 配合使用的所有框架和库。

<sup>2</sup>不适用

## <a name="next-steps"></a>后续步骤

- [生成和工作流配置](build-configuration.md)
