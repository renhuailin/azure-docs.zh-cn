---
title: 适用于 Application Insights JavaScript SDK 的 Angular 插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的 Angular 插件。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 7ac83d0c43026b431370fab1d8c49aec1adf6659
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312716"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>适用于 Application Insights JavaScript SDK 的 Angular 插件

适用于 Application Insights JavaScript SDK 的 Angular 插件支持：

- 跟踪路由器更改
- 跟踪未捕获的异常

> [!WARNING]
> Angular 插件与 ECMAScript 3 (ES3) 不兼容。

## <a name="getting-started"></a>入门

安装 npm 包：

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>基本用法

在应用的条目组件中设置 Application Insights 实例：

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

若要跟踪未捕获的异常，请在以下文件中设置 ApplicationinsightsAngularpluginErrorService`app.module.ts`：

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解 JavaScript SDK，请参阅 [Application Insights JavaScript SDK 文档](javascript.md)
- [GitHub 上的 Angular 插件](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
