---
title: 将 Azure 媒体资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 媒体资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: ac7cd5ca55525c11d2b985d931be24d1780899fb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117028958"
---
# <a name="migrate-media-resources-to-global-azure"></a>将媒体资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文提供的信息可帮助你将 Azure 媒体资源从 Azure 德国迁移到全球 Azure。

## <a name="media-services"></a>媒体服务

在 Azure 媒体服务中，你可以配置自己的存储帐户和所有媒体资产。 首先，在全球 Azure 中创建新的媒体服务帐户。 然后，重新加载相应的媒体项目，并在新的媒体服务帐户下执行编码和流式处理。

参考信息：

- 通过完成[媒体服务教程](../media-services/previous/index.yml)来更新你的知识。
- 查看[媒体服务概述](../media-services/previous/media-services-overview.md)。
- 了解如何[创建媒体服务帐户](../media-services/previous/media-services-portal-create-account.md)。

## <a name="media-player"></a>Media Player

可以在 Azure Media Player 中选择多个终结点。 可以从 Azure 德国终结点或全球 Azure 终结点流式传输内容。

有关详细信息，请参阅 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)。

## <a name="next-steps"></a>后续步骤

了解有关迁移以下服务类别中的资源的工具、技术和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)