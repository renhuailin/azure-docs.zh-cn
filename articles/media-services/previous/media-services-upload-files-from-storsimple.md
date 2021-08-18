---
title: 将文件从 Azure StorSimple 上传到 Azure 媒体服务帐户中 | Microsoft Docs
description: 本文简述 Azure StorSimple 数据管理器。 本文还提供了其他教程的链接，这些教程介绍了如何从 StorSimple 提取数据并将其作为资产上传到 Azure 媒体服务帐户。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9fb1dd99a86dcdda515d77d7a051ca88df26f089
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712129"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>将文件从 Azure StorSimple 上传到 Azure 媒体服务帐户中 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]
>
> 
> Azure StorSimple 数据管理器目前以个人预览版提供。 
> 

## <a name="overview"></a>概述

在媒体服务中，可以将数字文件上传到资产中。 资产可以包含视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件（以及这些文件的相关元数据。）上传文件完成后，相关内容即安全地存储在云中供后续处理和流式处理。

[Azure StorSimple](../../storsimple/index.yml) 使用云存储作为本地解决方案的扩展，可跨本地存储和云存储自动将数据分层。 在将数据发送到云之前，StorSimple 设备会删除数据中的重复项并压缩数据，因此能够以极高的效率将大型文件发送到云中。 [StorSimple 数据管理器](../../storsimple/storsimple-data-manager-overview.md)服务提供相应的 API，让你从 StorSimple 提取数据并以 AMS 资产的形式呈现数据。

## <a name="get-started"></a>入门

1. [创建媒体服务帐户](media-services-portal-create-account.md)，以便将资产传输到其中。
2. 根据 [StorSimple 数据管理器](../../storsimple/storsimple-data-manager-overview.md)一文中所述注册数据管理器预览版。
3. 创建 StorSimple 数据管理器帐户。
4. 创建数据转换作业。运行该作业可从 StorSimple 设备中提取数据并将其以资产形式传输到 AMS 帐户中。 

    开始运行该作业时，将创建存储队列。 此队列填充了可以使用的转换后 blob 的相关消息。 此队列的名称与作业定义的名称相同。 可以使用此队列确定资产何时就绪，并调用要针对资产运行的媒体服务操作。 例如，可以使用此队列来触发包含所需媒体服务代码的 Azure 函数。

## <a name="see-also"></a>请参阅

[在数据管理器中使用 .NET SDK 触发作业](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤

现即可编码已上传的资产。 有关详细信息，请参阅[对资产进行编码](media-services-portal-encode.md)。
