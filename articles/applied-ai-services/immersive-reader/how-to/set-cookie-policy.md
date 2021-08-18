---
title: 设置沉浸式阅读器 Cookie 策略
titleSuffix: Azure Applied AI Services
description: 本文将演示如何为沉浸式阅读器设置 Cookie 策略。
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 69cf0eb1659b243220eed1199dac06effc22f363
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778710"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何为沉浸式阅读器设置 Cookie 策略

默认情况下，沉浸式阅读器将禁用 Cookie 使用情况。 如果启用 Cookie 使用情况，则沉浸式阅读器可以使用 Cookie 来维护用户首选项并跟踪功能使用情况。 如果在沉浸式阅读器中启用 Cookie 使用情况，请考虑欧盟 Cookie 合规性策略的要求。 主机应用程序有责任根据欧盟 Cookie 合规性策略获得任何必要的用户许可。

可以通过沉浸式阅读器[选项](../reference.md#options)设置 Cookie 策略。

## <a name="enable-cookie-usage"></a>启用 Cookie 使用情况

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>禁用 Cookie 使用情况

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Android 教程](../how-to-launch-immersive-reader.md)，了解通过 Java 或 Kotlin for Android 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [iOS 教程](../how-to-launch-immersive-reader.md)，了解通过 Swift for iOS 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [Python 教程](../how-to-launch-immersive-reader.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)