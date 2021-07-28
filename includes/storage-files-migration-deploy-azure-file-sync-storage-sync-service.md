---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 180b615869579752f9a14aa2dcdd34f1676b577b
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2021
ms.locfileid: "109644846"
---
要完成此步骤，你需要 Azure 订阅凭据。

为 Azure 文件同步配置的核心资源称为存储同步服务。 建议只为当前或将来同步同一组文件的所有服务器部署一项存储同步服务。 仅当有不同的服务器组，且这些服务器永不交换数据时，才创建多项存储同步服务。 例如，你可能有一些绝不会同步同一个 Azure 文件共享的服务器。 否则，最佳做法是使用一项存储同步服务。

为存储同步服务选择一个离你的位置最近的 Azure 区域。 所有其他云资源必须部署在同一区域中。 若要简化管理，请在订阅中创建一个包含同步和存储资源的新资源组。

有关详细信息，请参阅有关部署 Azure 文件同步的文章中[关于部署存储同步服务的部分](../articles/storage/file-sync/file-sync-deployment-guide.md#deploy-the-storage-sync-service)。请严格按照这部分内容操作。 后续步骤中有这篇文章其他部分的链接。