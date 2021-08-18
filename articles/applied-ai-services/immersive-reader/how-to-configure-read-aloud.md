---
title: 配置大声朗读
titleSuffix: Azure Applied AI Services
description: 本文将向你介绍如何配置各种选项以进行大声朗读。
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 2694525c406a40a4c133eb3199d2222542905ae6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778578"
---
# <a name="how-to-configure-read-aloud"></a>如何配置大声朗读

本文演示如何在沉浸式阅读器中配置各种选项以进行大声朗读。

## <a name="automatically-start-read-aloud"></a>自动开始大声朗读

`options` 参数包含可用于配置大声朗读的所有标志。 将 `autoplay` 设置为 `true` 以在启动沉浸式阅读器后自动开始大声朗读。

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> 由于浏览器限制，Safari 中不支持自动播放。

## <a name="configure-the-voice"></a>配置语音

将 `voice` 设置为 `male` 或 `female`。 并非所有语言都支持这两种语音。 有关详细信息，请参阅[语言支持](./language-support.md)页。

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>配置播放速度

将 `speed` 设置为介于 `0.5` (50%) 和 `2.5` (250%)（含限值）之间的数字。 超出此范围的值将限制为 0.5 或 2.5。

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)