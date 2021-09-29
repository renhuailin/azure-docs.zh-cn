---
title: 适用于 Application Insights JavaScript SDK 的 React 插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的 React 插件。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: c95e408f116190bf90228f773a7d7c1530c6537e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217838"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>适用于 Application Insights JavaScript SDK 的 React 插件

适用于 Application Insights JavaScript SDK 的 React 插件，支持：

- 跟踪路由更改
- React 组件使用情况统计信息

## <a name="getting-started"></a>入门

安装 npm 包：

```bash

npm install @microsoft/applicationinsights-react-js @microsoft/applicationinsights-web --save

```

## <a name="basic-usage"></a>基本用法

初始化与 Application Insights 的连接：

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

使用高阶组件函数包装你的组件，对其启用 Application Insights：

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>配置

| 名称    | 默认 | 描述                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | React 路由器历史记录。 有关详细信息，请参阅 [react-router 包文件](https://reactrouter.com/web/api/history)。 若要了解如何访问组件外部的历史记录对象，请参阅 [Reac 路由器常见问题解答](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>React 组件使用情况跟踪

若要检测各种 React 组件使用情况跟踪，请应用 `withAITracking` 高阶组件函数。

它将测量从 `ComponentDidMount` 事件到 `ComponentWillUnmount` 事件的时间。 但是，为了使其更准确，它将减去用户空闲时间 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time`。

若要在 Azure 门户中查看此指标，需要导航到 Application Insights 资源，选择 “指标”选项卡并配置空图表以显示自定义指标名称“React 组件参与时间（秒）”，选择指标的聚合（求和、平均等），并应用拆分“组件名称”。

![显示自定义指标“React 组件参与时间（秒）”的图表的屏幕截图，该图表按“组件名称”拆分](./media/javascript-react-plugin/chart.png)

还可以运行自定义查询来划分 Application Insights 数据，以根据需求生成报表和可视化效果。 在 Azure 门户导航到 Application Insights 资源，从“概述”选项卡的顶部菜单中选择“分析”，然后运行查询。

![自定义指标查询结果的屏幕截图。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 新的自定义指标最多可能需要 10 分钟才能显示在 Azure 门户中。

## <a name="using-react-hooks"></a>使用 React 挂钩

[React 挂钩](https://reactjs.org/docs/hooks-reference.html)是一种在 React 应用程序中进行状态和生命周期管理的方法，无需依赖基于类的 React 组件。 Application Insights React 插件提供了许多挂钩集成，它们的运行方式与高阶组件方法类似。

### <a name="using-react-context"></a>使用 React 上下文

Application Insights React 挂钩旨在使用 [React 上下文](https://reactjs.org/docs/context.html)作为它的一个包含方面。 若要使用上下文，请如上所示初始化 Application Insights，然后导入上下文对象：

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

此上下文提供程序使 Application Insights 在它的所有子组件内可作为 `useContext` 挂钩使用。

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const metricData = {
        average: engagementTime,
        name: "React Component Engaged Time (seconds)",
        sampleCount: 1
      };
    const additionalProperties = { "Component Name": 'MyComponent' };
    appInsights.trackMetric(metricData, additionalProperties);
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric` 挂钩会复制 `withAITracking` 高阶组件的功能，而不会向组件结构中添加其他组件。 该挂钩采用两个参数，一个是 Application Insights 实例（可从 `useAppInsightsContext` 挂钩获取），另一个是用于跟踪的组件的标识符（如其名称）。

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

它的运行方式与高阶组件类似，但会响应挂钩生命周期事件，而不是组件生命周期。 如果需要在特定的交互上运行，则需要向用户事件显式提供挂钩。

### `useTrackEvent`

`useTrackEvent` 挂钩用于跟踪应用程序可能需要跟踪的任何自定义事件，例如按钮单击或其他 API 调用。 它采用四个参数：
-   Application Insights 实例（可以从 `useAppInsightsContext` Hook 获取）
-   事件的名称。
-   事件数据对象，该对象封装了必须跟踪的更改。
-   skipFirstRun（可选）标志，用于在初始化时跳过 `trackEvent` 调用。 默认值设置为 `true`。

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const [cart, setCart] = useState([]);
    const trackCheckout = useTrackEvent(appInsights, "Checkout", cart);
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated", cart);
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button></li>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button></li>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button></li>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button></li>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}

export default MyComponent;
```

使用该挂钩时，可以向其提供数据有效负载，以便在将事件存储到 Application Insights 时向该事件添加其他数据。

## <a name="react-error-boundaries"></a>React 错误边界

[错误边界](https://reactjs.org/docs/error-boundaries.html)提供一种在 React 应用程序中发生异常时妥善处理异常的方法，并且当发生此类错误时，很可能需要记录该异常。 Application Insights React 插件提供了一个错误边界组件，该组件将在发生错误时自动记录错误。

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

`AppInsightsErrorBoundary` 要求向它传递两个属性，一个是为应用程序创建的 `ReactPlugin` 实例，另一个是发生错误时要呈现一个组件。 发生未处理的错误时，使用提供给错误边界的信息调用 `trackException`，并且 `onError` 组件将显示。

## <a name="sample-app"></a>示例应用

查看 [Application Insights React 演示](https://github.com/Azure-Samples/application-insights-react-demo)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 JavaScript SDK，请参阅 [Application Insights JavaScript SDK 文档](javascript.md)。
- 若要了解 Kusto 查询语言和 Log Analytics 中的查询数据，请参阅[日志查询概述](../../azure-monitor/logs/log-query-overview.md)。
