---
title: 在沉浸式阅读器中显示数学内容
titleSuffix: Azure Applied AI Services
description: 本文将演示如何在沉浸式阅读器中显示数学内容。
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 0d4a0237d502cb353c262a40c92f99dfdbb8e7d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778440"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>如何在沉浸式阅读器中显示数学内容

沉浸式阅读器可以在以数学标记语言 ([MathML](https://developer.mozilla.org/docs/Web/MathML)) 的形式提供时显示数学内容。
可以通过沉浸式阅读器[区块](../reference.md#chunk)设置 MIME 类型。 有关详细信息，请参阅[支持的 MIME 类型](../reference.md#supported-mime-types)。

## <a name="send-math-to-the-immersive-reader"></a>向沉浸式阅读器发送数学内容
为了向沉浸式阅读器发送数学内容，请提供包含 MathML 的区块，并将 MIME 类型设置为 ```application/mathml+xml```；

例如，如果内容如下：

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

则可以使用以下 JavaScript 显示你的内容。

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

启动沉浸式阅读器时，应会看到：

![沉浸式阅读器中的数学内容](../media/how-tos/1-math.png)

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)