---
title: 配置大声朗读
titleSuffix: Azure Cognitive Services
description: 本文将向您介绍如何配置各种选项，以便大声读出。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 648227521e5e4e8feecd864d3e572a4758e551ca
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633257"
---
# <a name="how-to-configure-read-aloud"></a>如何配置大声朗读

本文演示了如何在沉浸式阅读器中配置各种选项来朗读。

## <a name="automatically-start-read-aloud"></a>自动开始朗读

`options`参数包含可用于配置大声朗读的所有标志。 设置 `autoplay` 为 `true` 可在启动沉浸式阅读器后自动开始朗读。

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> 由于浏览器限制，不支持在 Safari 中播放自动播放。

## <a name="configure-the-voice"></a>配置语音

设置 `voice` 为 `male` 或 `female` 。 并非所有语言都支持这两种语音。 有关详细信息，请参阅 [语言支持](./language-support.md) 页。

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>配置播放速度

设置 `speed` 为介于 `0.5` (50% ) 和 `2.5` (250% ) （含）之间的数字。 此范围外的值将限制为0.5 或2.5。

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)