---
title: 沉浸式阅读器 SDK 参考
titleSuffix: Azure Applied AI Services
description: 沉浸式阅读器 SDK 包含 JavaScript 库，它使你能够将沉浸式阅读器集成在应用程序中。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: 2ed5ac3717b992d41c3326037837e86e26f243f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778062"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>沉浸式阅读器 JavaScript SDK 参考 (v1.1)

沉浸式阅读器 SDK 包含 JavaScript 库，它使你能够将沉浸式阅读器集成在应用程序中。

## <a name="functions"></a>函数

SDK 会公开以下函数：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

在 Web 应用程序的 `iframe` 中启动沉浸式阅读器。 请注意，内容的大小限制为最大 50 MB。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync 参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| `token` | 字符串 | Azure AD 身份验证令牌。 有关更多详细信息，请参阅[如何创建沉浸式阅读器资源](./how-to-create-immersive-reader.md)。 |
| `subdomain` | 字符串 | Azure 中沉浸式阅读器资源的自定义子域。 有关更多详细信息，请参阅[如何创建沉浸式阅读器资源](./how-to-create-immersive-reader.md)。 |
| `content` | [内容](#content) | 一个对象，该对象包含要在沉浸式阅读器中显示的内容。 |
| `options` | [选项](#options) | 用于配置沉浸式阅读器的某些行为的选项。 可选。 |

#### <a name="returns"></a>返回

返回 `Promise<LaunchResponse>`，它可以解析加载沉浸式阅读器的时间。 `Promise` 会解析为 [`LaunchResponse`](#launchresponse) 对象。

#### <a name="exceptions"></a>例外

如果沉浸式阅读器加载失败，返回的 `Promise` 将被拒绝，并会出现 [`Error`](#error)。 有关详细信息，请参阅[错误代码](#error-codes)。

<br>

## <a name="close"></a>关闭

关闭沉浸式阅读器。

此函数的一个示例用例是通过设置“[选项](#options)”中的 ```hideExitButton: true``` 来确定退出按钮是否处于隐藏状态。 然后，可以在单击另一个按钮（例如，移动标头的后退箭头）时调用此 ```close``` 函数。

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>沉浸式阅读器启动按钮

SDK 为启动沉浸式阅读器的按钮提供默认样式。 使用 `immersive-reader-button` 类属性来启用此样式。 有关更多详细信息，请参阅[如何自定义沉浸式阅读器按钮](./how-to-customize-launch-button.md)。

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>可选属性

使用以下属性来配置按钮的外观。

| 属性 | 说明 |
| --------- | ----------- |
| `data-button-style` | 设置按钮的样式。 可以是 `icon`、`text` 或 `iconAndText`。 默认为 `icon`。 |
| `data-locale` | 设置区域设置。 例如，`en-US` 或 `fr-FR`。 默认为英语 `en`。 |
| `data-icon-px-size` | 设置图标的大小（以像素为单位）。 默认为 20px。 |

<br>

## <a name="renderbuttons"></a>renderButtons

如果使用[如何自定义沉浸式阅读器按钮](./how-to-customize-launch-button.md)指南，则不需要使用 ```renderButtons``` 函数。

此函数设置并更新文档的沉浸式阅读器按钮元素。 如果提供了 ```options.elements```，则这些按钮将在 ```options.elements``` 中提供的每个元素内呈现。 如果文档中有多个要启动沉浸式阅读器的部分，并且你想以一种简化的方式来呈现具有相同样式的多个按钮，或想要使用简单且一致的设计模式呈现按钮，则使用 ```options.elements``` 参数非常有用。 若要将此函数与 [renderButtons options](#renderbuttons-options) 参数一起使用，请在页面加载时调用 ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);```，如以下代码片段所示。 否则，这些按钮将在具有类 ```immersive-reader-button``` 的文档元素中呈现，如[如何自定义沉浸式阅读器按钮](./how-to-customize-launch-button.md)中所示。

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

有关更多呈现选项，请参阅上文中的[可选属性](#optional-attributes)。 若要使用这些选项，请将任何选项属性添加到页面 HTML 中的每个 ```HTMLDivElement``` 属性。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons 参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| `options` | [renderButtons 选项](#renderbuttons-options) | 用于配置 renderButtons 函数的某些行为的选项。 可选。 |

### <a name="renderbuttons-options"></a>renderButtons 选项

用于呈现沉浸式阅读器按钮的选项。

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons 选项参数

| 设置 | 类型 | 说明 |
| ------- | ---- | ----------- |
| 元素 | HTMLDivElement[] | 要在其中呈现沉浸式阅读器按钮的元素。 |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

包含对 `ImmersiveReader.launchAsync` 的调用的响应。 请注意，可以通过 `container.firstChild` 访问对包含沉浸式阅读器的 `iframe` 的引用。

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse 参数

| 设置 | 类型 | 说明 |
| ------- | ---- | ----------- |
| container | HTMLDivElement | 包含沉浸式阅读器 iframe 的 HTML 元素。 |
| sessionID | 字符串 | 此会话的全局唯一标识符，用于调试。 |
 
## <a name="error"></a>错误

包含有关错误的信息。

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>错误参数

| 设置 | 类型 | 说明 |
| ------- | ---- | ----------- |
| code | 字符串 | 一组错误代码的其中一个。 请参阅[错误代码](#error-codes)。 |
| message | 字符串 | 错误的用户可读表示形式。 |

#### <a name="error-codes"></a>错误代码

| 代码 | 说明 |
| ---- | ----------- |
| BadArgument | 提供的参数无效，请参阅[错误](#error)的 `message` 参数。 |
| 超时 | 沉浸式阅读器无法在指定的超时内加载。 |
| TokenExpired | 提供的令牌已过期。 |
| 已中止 | 超出了调用速率限制。 |

<br>

## <a name="types"></a>类型

### <a name="content"></a>内容

包含要在沉浸式阅读器中显示的内容。

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>内容参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| title | 字符串 | 沉浸式阅读器顶部显示的标题文本（可选） |
| 区块 | [[]](#chunk) | 区块数组 |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Chunk

单个数据区块，将传入沉浸式阅读器的内容。

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>区块参数

| 名称 | 类型 | 描述 |
| ---- | ---- |------------ |
| 内容 | 字符串 | 包含发送到沉浸式阅读器的内容的字符串。 |
| lang | 字符串 | 文本的语言，值为 IETF BCP 47 语言标记格式，例如 en、es-ES。 如果未指定，将自动检测语言。 请参阅[支持的语言](#supported-languages)。 |
| mimeType | 字符串 | 支持纯文本、MathML、HTML 和 Microsoft Word DOCX 格式。 有关更多详细信息，请参阅[支持的 MIME 类型](#supported-mime-types)。 |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>支持的 MIME 类型

| MIME 类型 | 说明 |
| --------- | ----------- |
| text/plain | 纯文本。 |
| text/html | HTML 内容。 [了解详细信息](#html-support)|
| application/mathml+xml | 数学标记语言 (MathML)。 [了解详细信息](./how-to/display-math.md)。
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word .docx 格式文档。


<br>

## <a name="options"></a>选项

包含配置沉浸式阅读器的某些行为的属性。

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>选项参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| uiLang | 字符串 | UI 的语言，值为 IETF BCP 47 语言标记格式，例如 en、es-ES。 如果未指定，则默认为浏览器语言。 |
| timeout | Number | [launchAsync](#launchasync) 失败并出现超时错误的持续时间（以毫秒为单位，默认值为 15000 ms）。 此超时仅适用于阅读器页面的初始启动，当阅读器页面打开且微调器启动时，会观察到已成功。 无需调整超时。 |
| uiZIndex | Number | 将创建的 iframe 的 Z-index（默认值为 1000）。 |
| useWebview | 布尔| 使用 Web 视图标记而不是 iframe，以便与 Chrome 应用兼容（默认值为 false）。 |
| onExit | 函数 | 沉浸式阅读器退出时执行。 |
| allowFullscreen | 布尔 | 切换全屏的功能（默认值为 true）。 |
| hideExitButton | 布尔 | 是否隐藏沉浸式阅读器的退出按钮箭头（默认值为 false）。 只有在提供了一种替代机制来退出沉浸式阅读器（例如移动工具栏的后退箭头）时，才应为 true。 |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | 沉浸式阅读器 cookie 使用情况的设置（默认为 CookiePolicy.Disable）。 主机应用程序有责任根据欧盟 Cookie 合规性策略获得任何必要的用户许可。 请参阅 [Cookie 策略选项](#cookiepolicy-options)。 |
| disableFirstRun | 布尔 | 禁用首次运行体验。 |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | 用于配置大声朗读的选项。 |
| translationOptions | [TranslationOptions](#translationoptions) | 用于配置翻译的选项。 |
| displayOptions | [DisplayOptions](#displayoptions) | 用于配置文本大小、字体等的选项。 |
| preferences | 字符串 | 从 onPreferencesChanged 返回的字符串，用于表示沉浸式阅读器中用户的首选项。 有关详细信息，请参阅[设置参数](#settings-parameters)和[如何存储用户首选项](./how-to-store-user-preferences.md)。 |
| onPreferencesChanged | 函数 | 在用户的首选项发生更改时执行。 有关详细信息，请参阅[如何存储用户首选项](./how-to-store-user-preferences.md)。 |
| customDomain | 字符串 | 保留以供内部使用。 托管沉浸式阅读器 Web 应用的自定义域（默认值为 null）。 |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> 重要说明：不要尝试以编程方式更改发送到沉浸式读取器应用程序或从中发出的 `-preferences` 字符串的值，因为这可能会导致意外行为，从而降低客户的用户体验。 主机应用程序不应将自定义值分配给 `-preferences` 字符串或操作该字符串。 使用 `-preferences` 字符串选项时，请仅使用从 `-onPreferencesChanged` 回调选项返回的确切值。

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions 参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| voice | 字符串 | 语音，“女性”或“男性”。 请注意，并非所有语言都支持这两种性别。 |
| 速度 | Number | 播放速度，必须介于 0.5 到 2.5 之间（含两者）。 |
| autoPlay | 布尔 | 当加载沉浸式读取器时，自动开始大声朗读。 |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> 由于浏览器限制，Safari 中不支持自动播放。

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions 参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| 语言 | 字符串 | 设置翻译语言，值为 IETF BCP 47 语言标记格式，例如 fr-FR、es-MX、zh-Hans-CN。 要求自动启用字词或文档翻译。 |
| autoEnableDocumentTranslation | 布尔 | 自动翻译整个文档。 |
| autoEnableWordTranslation | 布尔 | 自动启用字词翻译。 |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>DisplayOptions 参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| textSize | Number | 设置所选的文本大小。 |
| increaseSpacing | 布尔 | 设置开启还是关闭文本间距。 |
| fontFamily | 字符串 | 设置所选的字体（“Calibri”、“ComicSans”或“Sitka”）。 |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy 选项

```typescript
enum CookiePolicy { Disable, Enable }
```

下面列出的设置仅用于提供信息。 沉浸式阅读器将其设置或用户首选项存储在 cookie 中。 默认情况下，此 cookiePolicy 选项禁用 cookie，以符合欧盟 Cookie 合规性法律。 如果想要重新启用 cookie 并还原沉浸式阅读器用户首选项的默认功能，则需要确保网站或应用程序获得用户的适当许可，才能够启用 cookie。 然后，若要在沉浸式阅读器中重新启用 cookie，必须在启动沉浸式阅读器时将 cookiePolicy 选项显式设置为 CookiePolicy.Enable 。 下表描述了启用 cookiePolicy 选项时，沉浸式读取器在其 cookie 中存储的设置。

#### <a name="settings-parameters"></a>设置参数

| 设置 | 类型 | 说明 |
| ------- | ---- | ----------- |
| textSize | Number | 设置所选的文本大小。 |
| fontFamily | 字符串 | 设置所选的字体（“Calibri”、“ComicSans”或“Sitka”）。 |
| textSpacing | Number | 设置开启还是关闭文本间距。 |
| formattingEnabled | 布尔 | 设置开启还是关闭 HTML 格式设置。 |
| Theme — 主题 | 字符串 | 设置所选主题（例如“浅色”、“深色”）。 |
| syllabificationEnabled | 布尔 | 设置开启还是关闭音节划分。 |
| nounHighlightingEnabled | 布尔 | 设置开启还是关闭名词突出显示。 |
| nounHighlightingColor | 字符串 | 设置所选名词的突出显示颜色。 |
| verbHighlightingEnabled | 布尔 | 设置开启还是关闭谓词突出显示。 |
| verbHighlightingColor | 字符串 | 设置所选谓词的突出显示颜色。 |
| adjectiveHighlightingEnabled | 布尔 | 设置开启还是关闭形容词突出显示。 |
| adjectiveHighlightingColor | 字符串 | 设置所选形容词的突出显示颜色。 |
| adverbHighlightingEnabled | 布尔 | 设置开启还是关闭副词突出显示。 |
| adverbHighlightingColor | 字符串 | 设置所选副词的突出显示颜色。 |
| pictureDictionaryEnabled | 布尔 | 设置开启还是关闭图片字典。 |
| posLabelsEnabled | 布尔 | 设置开启还是关闭每个突出显示的词性的上标文本标签。  |

<br>

## <a name="supported-languages"></a>支持的语言

沉浸式阅读器的翻译功能支持多种语言。 请参阅[语言支持](./language-support.md)更多详细信息。

<br>

## <a name="html-support"></a>HTML 支持

启用格式设置时，以下内容将以 HTML 格式呈现在沉浸式阅读器中。

| HTML | 支持的内容 |
| --------- | ----------- |
| 字体样式 | 粗体、斜体、下划线、代码、删除线、上标、下标 |
| 无序列表 | 圆盘、圆形、方形 |
| 已排序列表 | 十进制、大写字母、小写字母、大写罗马字母、小写罗马字母 |

不受支持的标记将以类似的方式呈现。 目前不支持图像和表。

<br>

## <a name="browser-support"></a>浏览器支持

使用以下浏览器的最新版本，以获取沉浸式阅读器的最佳体验。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>后续步骤

* 探索 [GitHub 上的沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入门：创建可启动沉浸式阅读器的 Web 应用 (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
