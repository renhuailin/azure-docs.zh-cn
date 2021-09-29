---
title: Azure API 管理中的开发人员门户概述
titleSuffix: Azure API Management
description: 了解 API 管理中的开发人员门户 - 它是一个可自定义的网站，API 使用者可以在其中浏览你的 API。
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4fa9b0e777ce64322ebad74c8279724817530790
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664573"
---
# <a name="overview-of-the-developer-portal"></a>开发人员门户概述

开发人员门户是一个自动生成的、完全可自定义的网站，其中包含 API 的文档。 API 使用者可在其中找到 API、了解 API 的用法、请求访问权限以及试用这些 API。

如本文中所述，可以针对具体情况自定义和扩展开发人员门户。 

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>从旧门户迁移

> [!IMPORTANT]
> 旧开发人员门户现在已弃用，并且将仅接收安全更新。 你可以像往常一样继续使用它，直到它在 2023 年 10 月停用，届时将从所有 API 管理服务中将其删除。

[专用文档文章](developer-portal-deprecated-migration.md)中介绍了到新开发人员门户的迁移。

## <a name="customization-and-styling"></a>自定义和样式

可通过内置的拖放可视化编辑器对开发人员门户进行自定义和样式设置。 有关更多详细信息，请参阅[本教程](api-management-howto-developer-portal-customize.md)。

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> 扩展性

API 管理服务包括始终保持最新的内置托管开发人员门户。 可以从 Azure 门户界面访问它。

如果需要用自定义逻辑（不支持开箱即用）来扩展它，可以修改其代码库。 门户的代码库在 [GitHub 存储库中可用](https://github.com/Azure/api-management-developer-portal)。 例如，你可以实现与第三方支持系统集成的新的小组件。 实现新功能时，可以选择以下选项之一：

- 在 API 管理服务之外自行承载生成的门户。 当你自行承载门户时，你将成为门户的维护人员并负责其升级。 Azure 支持的协助仅限于[自承载门户的基本设置](developer-portal-self-host.md)。
- 为 API 管理团队打开拉取请求，以将新功能合并到托管门户的代码库。

有关扩展性的详细信息和说明，请参阅 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal)和有关[实现小组件](developer-portal-implement-widgets.md)的教程。 [自定义托管门户](api-management-howto-developer-portal-customize.md)的教程介绍了门户的管理面板，该面板在托管版本和自承载版本中很常见 。


## <a name="next-steps"></a>后续步骤

详细了解新开发人员门户：

- [访问和自定义托管开发人员门户](api-management-howto-developer-portal-customize.md)
- [设置自承载版本的门户](developer-portal-self-host.md)
- [实现自己的小组件](developer-portal-implement-widgets.md)

浏览其他资源：

- [包含源代码的 GitHub 存储库](https://github.com/Azure/api-management-developer-portal)
- [有关开发人员门户的常见问题](developer-portal-faq.md)
