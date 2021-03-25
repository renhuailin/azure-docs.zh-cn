---
title: 适用于 Application Insights JavaScript SDK 的“单击分析自动收集”插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的“单击分析自动收集”插件。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e48d669321ad8c58681e8a92e68f2089962bdc17
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429844"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>适用于 Application Insights JavaScript SDK 的“单击分析自动收集”插件

此插件可自动跟踪网页上的单击事件，并使用 HTML 元素上的 data-* 属性来填充事件遥测数据。

## <a name="getting-started"></a>入门

用户可通过 npm 设置“单击分析自动收集”插件。

### <a name="npm-setup"></a>npm 设置

安装 npm 包：

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>如何有效使用此插件

1. 从单击事件生成的遥测数据将作为 `customEvents` 存储在 Azure 门户的 Application Insights 部分。
2. 系统根据以下规则填充 customEvent 的 `name`：
    1.  `data-*-id` 中提供的 `id` 将用作 customEvent 名称。 例如，如果单击的 HTML 元素的“data-sample-id”=“button1”，则“button1”将为 customEvent 名称。
    2. 如果不存在这样的属性，并且配置中的 `useDefaultContentNameOrId` 设置为 `true`，则所选元素的 HTML 属性 `id` 或元素的内容名称将用作 customEvent 名称。
    3. 如果 `useDefaultContentNameOrId` 为 false，则 customEvent 名称将为“not_specified”。

    > [!TIP]
    > 建议将 `useDefaultContentNameOrId` 设置为 true，以生成有意义的数据。  
3. `parentDataTag` 执行两项操作：
    1. 如果此标记存在，此插件将从单击的元素的所有 HTML 父元素中提取 `data-*` 属性和值。
    2. 为提升效率，该插件会将此标记用作标志，遇到此标记时，它将阻止自己向上处理 DOM（文档对象模型）。
    
    > [!CAUTION]
    > 使用 `parentDataTag` 后，SDK 将开始在整个应用程序中查找父标记，而不只是在使用它的 HTML 元素中查找。
4. 用户提供的 `customDataPrefix` 应始终以 `data-` 开头，例如 `data-sample-`。 在 HTML 中，`data-*` 全局属性构成一类允许通过脚本在 HTML 及其 DOM 表示形式之间交换专有信息的属性，称为“自定义数据属性”。 旧版浏览器（Internet Explorer、Safari）会删除它不理解的属性，除非属性以 `data-` 开头。

    `data-*` 中的 `*` 可能会被遵循 [XML 名称生成规则](https://www.w3.org/TR/REC-xml/#NT-Name)的任意名称替换，但有以下限制：
    - 名称不能以“xml”开头，无论这些字母是使用大写还是小写都是如此。
    - 名称不能包含任何分号 (U+003A)。
    - 名称不能包含大写字母。

## <a name="configuration"></a>配置

| 名称                  | 类型                               | 默认 | 说明                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | 是    | 自动捕获配置。                                                                                                         |
| 回调 (callback)              | [IValueCallback](#ivaluecallback)  | NULL    | 回调配置。                                                                                                                 |
| pageTags              | string                             | NULL    | 页标记。                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| NULL    | 提供的自定义数据标记用于替代捕获单击数据的默认标记。                                                           |
| urlCollectHash        | boolean                            | false   | 启用 URL中“#”字符后的值的日志记录。                                                                          |
| urlCollectQuery       | boolean                            | false   | 启用 URL 查询字符串的日志记录。                                                                                      |
| behaviorValidator     | 功能                           | NULL  | 用于验证 `data-*-bhvr` 值的回调函数。 有关详细信息，请参阅 [behaviorValidator 部分](#behaviorvalidator)。|
| defaultRightClickBhvr | 字符串（或）数字                 | ''      | 发生“右键单击”事件时的默认行为值。 如果元素具有 `data-*-bhvr` 属性，则将替代此值。 |
| dropInvalidEvents     | boolean                            | false   | 用于删除无有用单击数据的事件的标志。                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| 名称               | 类型     | 默认 | 说明                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | 功能 | NULL    | 用于替代默认 pageName 捕获行为的函数。                           |
| pageActionPageTags | 功能 | NULL    | 用于增加在 pageAction 事件期间收集的默认 pageTags 的回调函数。  |
| contentName        | 功能 | NULL    | 用于填充自定义 contentName 的回调函数。                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| 名称                      | 类型    | 默认   | 要在 HTML 中使用的默认标记 |   说明                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | 不适用         |当特定元素未使用默认的 customDataPrefix 进行标记或用户未提供 customDataPrefix 时，收集 contentName 的标准 HTML 属性。 |
| customDataPrefix          | string  | `data-`   | `data-*`| 自动捕获使用所提供的前缀标记的元素的内容名称和值。 例如，可以在 HTML 标记中使用 `data-*-id`、`data-<yourcustomattribute>`。   |
| aiBlobAttributeTag        | string  | `ai-blob` |  `data-ai-blob`| 插件支持 JSON blob 属性，而不支持单个 `data-*` 属性。 |
| metaDataPrefix            | string  | NULL      | 不适用  | 捕获时，自动捕获 HTML 标头的 meta 元素名称和带有所提供的前缀的内容。 例如，可以在 HTML meta 标记中使用 `custom-`。 |
| captureAllMetaDataContent | boolean | false     | 不适用   | 自动捕获所有 HTML 标头的 meta 元素名称和内容。 默认值为 false。 如果启用此参数，则将替代所提供的 metaDataPrefix。 |
| parentDataTag             | string  | NULL      |  不适用  | 遇到此标记时，将停止遍历 DOM，不再捕获元素的内容名称和值。 例如，可以在 HTML 标记中使用 `data-<yourparentDataTag>`。|
| dntDataTag                | string  | `ai-dnt`  |  `data-ai-dnt`| 捕获遥测数据的插件将忽略具有此属性的 HTML 元素。|

### <a name="behaviorvalidator"></a>behaviorValidator

BehaviorValidator 函数会自动检查代码中标记的行为是否符合预定义的列表。 这样可以确保标记的行为与企业建立的分类一致。 大多数 Azure Monitor 客户不需要使用，预计也不会使用该函数，但它可以用于高级场景。 有三个不同的 behaviorValidator 回调函数作为此扩展的一部分公开。 但如果公开的函数不能满足需求，用户可以使用自己的回调函数。 这样做的目的是引入你自己的行为数据结构，当从数据标签中提取行为时，插件将使用此验证程序函数。

| 名称                   | 说明                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | 如果行为数据结构是字符串数组，请使用此回调函数。|
| BehaviorMapValidator   | 如果行为数据结构为字典，则使用此回调函数。       |
| BehaviorEnumValidator  | 如果行为数据结构是一个枚举，则使用此回调函数。            |

#### <a name="sample-usage-with-behaviorvalidator"></a>behaviorValidator 的示例用法

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>示例应用

[启用了“单击分析自动收集”插件的简单 Web 应用](https://go.microsoft.com/fwlink/?linkid=2152871)。

## <a name="next-steps"></a>后续步骤

- 查看“单击分析自动收集”插件的 [GitHub 存储库](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js)和 [NPM 包](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js)。
- 利用[使用体验中的事件分析](usage-segmentation.md)，按可用维度分析热门单击和切片。
- 在 [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query) CustomEvents 表的 customDimensions 中，查找内容字段下的单击数据。 如需查看其他指南，请参阅[示例应用](https://go.microsoft.com/fwlink/?linkid=2152871)。
- 生成[工作簿](../visualize/workbooks-overview.md)或[导出到 Power BI](../logs/log-powerbi.md#integrating-queries)，以创建单击数据的自定义可视化效果。
