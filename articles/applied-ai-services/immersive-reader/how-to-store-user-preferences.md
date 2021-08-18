---
title: 存储用户首选项
titleSuffix: Azure Applied AI Services
description: 本文将演示如何存储用户的首选项。
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 988b6aa7445eb5080d0c67c1af997bcbd046d4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778415"
---
# <a name="how-to-store-user-preferences"></a>如何存储用户首选项

本文演示如何通过 [-preferences](./reference.md#options) 和 [-onPreferencesChanged](./reference.md#options) 沉浸式阅读器 SDK 选项存储用户的 UI 设置（正式名称为用户首选项）。

当 [CookiePolicy](./reference.md#cookiepolicy-options) SDK 选项设置为“已启用”时，沉浸式阅读器应用程序会将“用户首选项”（文本大小、主题颜色、字体等）存储在 Cookie 中，Cookie 对特定游览器和设备而言是本地的。 每当用户在同一浏览器和设备上启动沉浸式阅读器时，它都会按照用户在该设备上最后一次会话的用户首选项打开。 但是，如果用户在其他浏览器或设备上打开沉浸式阅读器，则最初将根据沉浸式阅读器的默认设置来配置这些设置，然后用户必须为要使用的每个设备重新设置其首选项，以此类推。 `-preferences` 和 `-onPreferencesChanged` 沉浸式阅读器 SDK 选项为应用程序提供了一种在各种浏览器和设备上漫游用户首选项的方法，这样无论用户在何处使用应用程序都能获得一致的体验。

首先，通过在启动沉浸式阅读器应用程序时提供 `-onPreferencesChanged` 回调 SDK 选项，每当用户在沉浸式阅读器会话期间更改其首选项时，沉浸式阅读器都会将 `-preferences` 字符串发送回主机应用程序。 然后，主机应用程序负责在其自己的系统中存储用户首选项。 接着，当同一用户再次启动沉浸式阅读器时，主机应用程序可以从存储中检索该用户的首选项，并在启动沉浸式阅读器应用程序时将其作为 `-preferences` 字符串 SDK 选项提供，以便还原用户的首选项。

如果使用 Cookie 不可取或不可行，则可以使用此功能作为存储用户首选项的替代方法。

> [!CAUTION]
> 重要说明：不要尝试以编程方式更改发送到沉浸式读取器应用程序或从中发出的 `-preferences` 字符串的值，因为这可能会导致意外行为，从而降低客户的用户体验。 主机应用程序不应将自定义值分配给 `-preferences` 字符串或操作该字符串。 使用 `-preferences` 字符串选项时，请仅使用从 `-onPreferencesChanged` 回调选项返回的确切值。

## <a name="how-to-enable-storing-user-preferences"></a>如何启用存储用户首选项

沉浸式阅读器 SDK [launchAsync](./reference.md#launchasync) `options` 参数包含 `-onPreferencesChanged` 回调。 每当用户更改其首选项时，都将调用此函数。 `value` 参数包含一个字符串，该字符串表示用户的当前首选项。 然后，由主机应用程序为该用户存储此字符串。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>如何将用户首选项加载到沉浸式阅读器

使用 `-preferences` 选项将用户的首选项传递给沉浸式阅读器。 一个存储和加载用户首选项的普通示例如下所示：

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)