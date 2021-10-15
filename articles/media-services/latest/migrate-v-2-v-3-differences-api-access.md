---
title: 媒体服务 V2 与 v3 API 访问
description: 本文介绍 Azure 媒体服务 V2 与 V3 之间的 API 访问差异。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 594f4d68669b216e4606806a9ac3ef9a5b2f0b5c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358299"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Azure 媒体服务 V2 与 V3 API 之间的 API 访问差异

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-2.svg)

本文介绍 Azure 媒体服务 V2 与 V3 之间的 API 访问差异。

## <a name="api-access"></a>API 访问

所有媒体服务帐户都将有权访问 V3 API。 但是，我们强烈建议在将更新的代码应用到现有 V2 帐户之前，在新帐户上进行迁移开发。 这是因为 V3 实体不向后兼容 V2。 某些 V2 实体（如资产）可以向前兼容 V3。
如果不混合使用 V2 和 V3 API，然后尝试返回 V2，则可以继续使用现有帐户，但是不建议这样做。

在 2024 年 V2 API 停用前都可以对其进行访问。

## <a name="create-a-v3-account"></a>创建 V3 帐户

迁移时，可以创建仍有权访问 V2 的 V3 帐户。  可以通过以下方式创建帐户：

- REST API 和更早版本
- 选择门户中的复选框。

> [!div class="mx-imgBorder"]
> [ ![在门户中创建帐户](./media/migration-guide/v-3-v-2-access-account-creation-small.png) ](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

所有 .NET、CLI 和其他 SDK 都将面向最新的 2020-05-01 API，因此请查找或配置较旧的 API 版本。

> [!NOTE]
> 使用 2020-05-01（或更新）版本 API 创建的新帐户不能使用 V2 API。
