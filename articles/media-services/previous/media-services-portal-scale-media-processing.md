---
title: 使用 Azure 门户缩放媒体处理 | Microsoft Docs
description: 本教程逐步介绍了如何使用 Azure 门户缩放媒体处理。
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1040ffa8bc3af3734a5c37961676b0914f72e991
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835869"
---
# <a name="change-the-reserved-unit-type"></a>更改预留单位类型

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概述

默认情况下，Azure 媒体服务不再需要使用媒体保留单位，也不再支持。 出于兼容性考虑，当前 Azure 门户提供了一个管理和缩放 MRU 的选项。 但是，默认情况下，不会使用你设置的任何 MRU 配置来控制编码的并发或性能。

> [!IMPORTANT]
> 请确保查看[概述](media-services-scale-media-processing-overview.md)主题，获取有关调整媒体处理的规模主题的详细信息。

## <a name="scale-media-processing"></a>调整媒体处理的规模
>[!NOTE]
>选择 MRU 不会影响 Azure 媒体服务 V3 中的并发或性能。 

若要更改预留单位类型和预留单位数目，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“媒体预留单位”。
   
    若要更改所选预留单位类型的预留单位数，请使用屏幕顶部的“媒体预留单位”滑块。
   
    若要更改“预留单位类型”，请单击“预留处理单位的速度”栏。 然后，选择所需的定价层：S1、S2 或 S3。
   
3. 按“保存”按钮保存更改。
   
    按“保存”后，会立即分配新的预留单位。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
