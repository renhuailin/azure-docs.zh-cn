---
title: Azure Media Player 概述
description: 详细了解 Azure Media Player。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: faf5bc8a96cb3781ed1d48e9d7f0d1fd7cbc386e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679572"
---
# <a name="azure-media-player-overview"></a>Azure Media Player 概述 #

Azure Media Player 是一种 Web 视频播放器，用于在各种浏览器和设备中播放 [Microsoft Azure 媒体服务](https://azure.microsoft.com/services/media-services/)中的媒体内容。 Azure Media Player 使用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。  如果无法在设备或浏览器中提供这些标准，Azure 媒体播放器会采用 Flash 和 Silverlight 作为回退技术。 无论使用何种播放技术，开发人员都可使用统一的 JavaScript 接口来访问 API，从而允许 Azure 媒体服务提供的内容在各种设备和浏览器中播放，而无需进行任何额外的操作。

Microsoft Azure 媒体服务允许播放使用 DASH、平滑流式处理和 HLS 流格式提供的内容。 Azure 媒体播放器会考虑这些不同的格式并基于平台/浏览器功能自动播放最佳链接。 Microsoft Azure 媒体服务还允许使用通用加密（PlayReady 或 Widevine）或 AES-128 位信封加密对资产进行动态加密。 Azure 媒体播放器在合理配置时允许对 PlayReady 和 AES-128 位加密的内容进行解密。  若要了解如何配置播放器，请参阅[受保护内容](azure-media-player-protected-content.md)部分。

如果你有具体问题、疑问或发现任何 bug，请使用客户端播放类别[提交支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

> [!NOTE]
> 请注意，Azure Media Player 仅支持来自 Azure 媒体服务的媒体流。

## <a name="license"></a>许可 ##

Azure Media Player 已获得许可，并受适用于 Azure Media Player 的 Microsoft 软件许可条款中列出的条款约束。 有关完整条款，请参阅[许可证文件](/legal/azure-media-player/azure-media-player-license)。 有关详细信息，请参阅[隐私声明](https://www.microsoft.com/en-us/privacystatement/default.aspx)。

版权所有 2015 Microsoft Corporation。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
