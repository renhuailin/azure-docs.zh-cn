---
title: Azure 媒体服务 V2 到 v3 API 访问差异
description: 本文介绍 Azure 媒体服务 V2 与 V3 之间的 API 访问差别。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 59347d2ff797cbbd2f91410c3ec713b544d7fd08
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928214"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Azure 媒体服务 V2 与 v3 API 之间的 API 访问差异

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-2.svg)

本文介绍 Azure 媒体服务 V2 与 V3 之间的 API 访问差别。

## <a name="api-access"></a>API 访问

所有媒体服务帐户都将有权访问 V3 API。 但是，我们强烈建议在将更新的代码应用到现有 V2 帐户之前，在新帐户上进行迁移开发。 这是因为 V3 实体不向后兼容 V2。 某些 V2 实体（如资产）向前兼容 V3。
如果未混合 V2 和 V3 Api，则可继续使用现有帐户，但不建议这样做。

在2024中停用后，才可以访问 V2 API。

迁移时，可以创建仍有权访问 V2 的 V3 帐户。  可以通过以下方式创建帐户：

- REST API 和更早版本
- 选择门户中的复选框。

> [!div class="mx-imgBorder"]
> [![在门户 ](./media/migration-guide/v-3-v-2-access-account-creation-small.png) 中创建帐户](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

所有 .NET、CLI 和其他 Sdk 都将面向最新的 2020-05-01 API，因此需要查找或配置旧的 API 版本。

> [!NOTE]
> 使用 2020-05-01 API 创建的新帐户不能使用 V2 Api。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
