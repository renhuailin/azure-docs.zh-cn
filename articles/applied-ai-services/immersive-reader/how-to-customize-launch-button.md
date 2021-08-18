---
title: 编辑沉浸式阅读器启动按钮
titleSuffix: Azure Applied AI Services
description: 本文将介绍如何自定义启动沉浸式阅读器的按钮。
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: 1f5feff0857b98b077ed1e19dbb7aaa82004df69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778443"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>如何自定义沉浸式阅读器按钮

本文演示了如何自定义启动沉浸式阅读器的按钮，以满足应用程序的需求。

## <a name="add-the-immersive-reader-button"></a>添加沉浸式阅读器按钮

沉浸式阅读器 SDK 为启动沉浸式阅读器的按钮提供默认样式。 使用 `immersive-reader-button` 类特性启用此样式。

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>自定义按钮样式

使用 `data-button-style` 属性设置按钮的样式。 允许值为 `icon`、`text` 和 `iconAndText`。 默认值为 `icon`。

### <a name="icon-button"></a>图标按钮

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

这会呈现以下内容：

![这是呈现的文本按钮](./media/button-icon.png)

### <a name="text-button"></a>文本按钮

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

这会呈现以下内容：

![这是呈现的沉浸式阅读器按钮。](./media/button-text.png)

### <a name="icon-and-text-button"></a>图标和文本按钮

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

这会呈现以下内容：

![图标按钮](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>自定义按钮文本

使用 `data-locale` 特性为按钮配置语言和替换文本。 默认语言为英语。

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>自定义图标大小

可使用 `data-icon-px-size` 特性配置沉浸式阅读器图标大小。 这会设置图标的大小（以像素为单位）。 默认大小为 20px。

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)