---
title: 基于内容保护方案的迁移指南 |Microsoft Docs
description: 本文提供了基于内容保护方案的指南，可帮助你将 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a546120a93f311be29083d5f23d4716316bf64f4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690245"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>基于内容保护方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-4.svg)

本文提供了基于内容保护方案的指南，可帮助你将 Azure 媒体服务 v2 迁移到 v3。

## <a name="protect-content-in-v3-api"></a>在 v3 API 中保护内容

使用新的 v3 API 中的 [多项](design-multi-drm-system-with-access-control.md) 功能支持。

有关具体步骤，请参阅内容保护概念、教程和操作方法指南。

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>内容保护概念、教程和操作方法指南

### <a name="concepts"></a>概念

- [使用媒体服务动态加密保护内容](content-protection-overview.md)
- [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)
- [带有 PlayReady 许可证模板的媒体服务 v3](playready-license-template-overview.md)
- [带有 Widevine 许可证模板的媒体服务 v3 概述](widevine-license-template-overview.md)
- [Apple FairPlay 许可要求和配置](fairplay-license-overview.md)
- [流式处理策略](streaming-policy-concept.md)
- [内容密钥策略](content-key-policy-concept.md)

### <a name="tutorials"></a>教程

[快速入门：使用门户加密内容](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>操作方法指南

- [从现有策略获取签名密钥](get-content-key-policy-dotnet-howto.md)
- [适用于 iOS 的脱机 FairPlay 流式处理与媒体服务 v3](offline-fairplay-for-ios.md)
- [适用于 Android 的脱机 Widevine 流式处理媒体服务 v3](offline-widevine-for-android.md)
- [使用媒体服务 v3 进行适用于 Windows 10 的 PlayReady 脱机流式处理](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>示例

你还可以 [在代码示例中比较 V2 和 V3 代码](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]