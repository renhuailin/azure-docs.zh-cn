---
title: 如何为沉浸式阅读器准备 HTML 内容
titleSuffix: Azure Applied AI Services
description: 了解如何使用 HTML、JavaScript、Python、Android 或 iOS 启动沉浸式阅读器。 沉浸式阅读器使用可靠的技术来提高语言学习者、萌发期读者和具有学习差异的学生的阅读理解力。
author: erhopf
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 4efadfaed4980afd035f36d530f87a971eeb4964
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777926"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>如何为沉浸式阅读器准备 HTML 内容

本文说明如何构建 HTML 和检索内容，使其可供沉浸式阅读器使用。

## <a name="prepare-the-html-content"></a>准备 HTML 内容

将想要呈现的内容置于容器元素内的沉浸式阅读器中。 确保容器元素具有唯一的 `id`。 沉浸式阅读器提供对基本 HTML 元素的支持，有关详细信息，请参见[参考](reference.md#html-support)。

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>获取 JavaScript 中的 HTML 内容

使用容器元素的 `id` 来获取 JavaScript 代码中的 HTML 内容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>启动沉浸式阅读器以显示 HTML 内容

调用 `ImmersiveReader.launchAsync` 时，将区块的 `mimeType` 属性设置为 `text/html` 以启用呈现 HTML。

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](reference.md)