---
title: 适用于 Application Insights JavaScript SDK 的 React Native 插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的 React Native 插件。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 6b407fdc0614d94862257ce3bebc0f58f971f9d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735833"
---
# <a name="react-native-plugin-for-application-insights-javascript-sdk"></a>适用于 Application Insights JavaScript SDK 的 React Native 插件

Application Insights JavaScript SDK 的 React Native 插件收集设备信息，默认情况下此插件会自动收集以下信息：

- 唯一设备 ID（也称为安装 ID。）
- 设备型号名称（如 iPhone X、Samsung Galaxy Fold、Huawei P30 Pro 等。）
- 设备类型（例如手持设备、平板电脑等。）

## <a name="requirements"></a>要求

必须使用等于或高于 2.0.0 版本的 `@microsoft/applicationinsights-web`。 此插件将仅在 react-native 应用中工作。 它将不使用[使用 Expo 框架的应用](https://docs.expo.io/)，因此它无法与 Create React Native 应用一起使用。

## <a name="getting-started"></a>入门

安装并链接 [react-native-device-info](https://www.npmjs.com/package/react-native-device-info) 包。 使 `react-native-device-info` 包保持最新，以使用应用收集最新的设备名称。

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>初始化插件

若要使用此插件，你需要构造该插件并将其作为 `extension` 添加到现有的 Application Insights 实例中。

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>后续步骤

- 若要详细了解 JavaScript SDK，请参阅 [Application Insights JavaScript SDK 文档](javascript.md)。
- 若要了解 Kusto 查询语言和 Log Analytics 中的查询数据，请参阅[日志查询概述](../../azure-monitor/logs/log-query-overview.md)。
