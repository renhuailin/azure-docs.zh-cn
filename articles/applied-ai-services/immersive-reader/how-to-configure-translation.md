---
title: 配置翻译
titleSuffix: Azure Applied AI Services
description: 本文将向你介绍如何配置各种选项以进行翻译。
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: e8fb21ac286997601ae8896616826651bad8b1f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778549"
---
# <a name="how-to-configure-translation"></a>如何配置翻译

本文演示如何在沉浸式阅读器中配置各种选项以进行翻译。

## <a name="configure-translation-language"></a>配置翻译语言

`options` 参数包含可用于配置翻译的所有标志。 将 `language` 参数设置为要翻译成的语言。 有关受支持语言的完整列表，请参阅[语言支持](./language-support.md)。

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>加载时自动翻译文档

将 `autoEnableDocumentTranslation` 设置为 `true` 以在沉浸式阅读器加载时自动翻译整个文档。

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>自动启用单词翻译

将 `autoEnableWordTranslation` 设置为 `true` 以启用单字翻译。

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)