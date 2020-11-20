---
title: Application Insights JavaScript SDK 的响应插件
description: 如何安装和使用 Application Insights JavaScript SDK 的响应插件。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981079"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的响应插件

Application Insights JavaScript SDK 的响应插件，启用：

- 跟踪路由更改
- 反应组件使用统计信息

## <a name="getting-started"></a>入门

安装 npm 包：

```bash

npm install @microsoft/applicationinsights-react-js

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

使用高阶组件函数包装你的组件，以便在其上启用 Application Insights：

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>Configuration

| 名称    | 默认 | 说明                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | 对路由器历史记录做出响应。 有关详细信息，请参阅 [响应路由器包文档](https://reactrouter.com/web/api/history)。 若要了解如何访问组件以外的历史记录对象，请参阅 [响应-路由器常见问题解答](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>对组件使用情况跟踪做出反应

若要检测各种反应组件使用情况跟踪，请应用 `withAITracking` 高阶组件函数。

它将 `ComponentDidMount` 通过事件度量事件的时间 `ComponentWillUnmount` 。 但是，为了使此方法更精确，它会减去用户处于空闲状态的时间 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` 。

若要在 Azure 门户中查看此指标，你需要导航到 Application Insights 资源，选择 "指标" 选项卡，并配置空图表以显示自定义指标名称 "响应组件占用时间 (秒") "，选择聚合 (总和，avg 等，并将 split 应用为" Component Name "。

![显示自定义指标的图表屏幕截图，"响应组件占用时间 (秒) " 拆分为 "组件名称"](./media/javascript-react-plugin/chart.png)

您还可以运行自定义查询，以根据您的要求来划分 Application Insights 数据以生成报表和可视化效果。 在 Azure 门户导航到 Application Insights 资源，从 "概述" 选项卡的顶部菜单中选择 "分析"，然后运行查询。

![自定义指标查询结果的屏幕截图。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 新的自定义指标可能需要长达10分钟的时间才能显示在 Azure 门户中。

## <a name="using-react-hooks"></a>使用响应挂钩

[反应挂钩](https://reactjs.org/docs/hooks-reference.html) 是在响应应用程序中进行状态和生命周期管理的一种方法，无需依赖基于类的反应组件。 Application Insights 响应插件提供了许多挂钩集成，它们的操作方式与高阶组件方法类似。

### <a name="using-react-context"></a>使用反应上下文

Application Insights 的响应挂钩旨在将 [反应上下文](https://reactjs.org/docs/context.html) 用作包含它的方面。 若要使用上下文，请初始化如上所示的 Application Insights，然后导入上下文对象：

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

此上下文提供程序将使 Application Insights 在 `useContext` 它的所有子组件内作为挂钩提供。

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric`挂钩会复制高阶组件的功能 `withAITracking` ，而不会向组件结构添加其他组件。 该挂钩采用两个参数，第一个参数是 Application Insights 实例 (可从挂钩) 获取该实例 `useAppInsightsContext` ，该实例的标识符用于跟踪 (如名称) 。

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

它将像高阶组件一样运行，但会响应挂钩生命周期事件，而不是组件生命周期。 如果需要在特定交互中运行，则需要向用户事件显式提供挂钩。

### `useTrackEvent`

`useTrackEvent`挂钩用于跟踪应用程序可能需要跟踪的任何自定义事件，如按钮单击或其他 API 调用。 它采用两个参数，第一个参数是 Application Insights 实例 (可从挂钩) 获取该实例 `useAppInsightsContext` ，并为事件的名称。

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
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
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

使用挂钩时，可以向其提供数据负载，以便在将数据存储到 Application Insights 时向事件添加其他数据。

## <a name="react-error-boundaries"></a>响应误差边界

[误差边界](https://reactjs.org/docs/error-boundaries.html) 提供一种在响应应用程序中发生异常的情况，并在发生此类错误时，可能需要记录异常。 Application Insights 的响应插件提供一个错误边界组件，该组件将在发生错误时自动记录错误。

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

`AppInsightsErrorBoundary`需要向它传递两个属性，为 `ReactPlugin` 应用程序创建的实例和发生错误时要呈现的组件。 发生未处理的错误时， `trackException` 将使用提供给错误边界的信息调用，并 `onError` 显示组件。

## <a name="sample-app"></a>示例应用

查看 [Application Insights 反应演示](https://github.com/Azure-Samples/application-insights-react-demo)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 JavaScript SDK 的详细信息，请参阅 [Application Insights JAVASCRIPT sdk 文档](javascript.md)。
- 若要了解 Kusto 查询语言和查询 Log Analytics 中的数据，请参阅 [日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。
