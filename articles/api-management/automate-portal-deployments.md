---
title: 自动执行开发人员门户部署
titleSuffix: Azure API Management
description: 了解如何在两个 API 管理服务之间自动迁移自承载开发人员门户内容。
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a2fab946179fb0bef9db0068bb8f660f91ca8c04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669144"
---
# <a name="automate-developer-portal-deployments"></a>自动执行开发人员门户部署

API 管理开发人员门户支持以编程方式访问内容。 它允许你通过[内容管理 REST API](/rest/api/apimanagement/) 将数据导入或导出 API 管理服务。 REST API 访问适用于托管门户和自承载门户。

## <a name="automated-migration-script"></a>自动迁移脚本

可以使用 API 在两个 API 管理服务之间自动迁移内容（例如，测试环境中的服务和生产环境中的服务）。 API 管理开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js)中的 `scripts.v3/migrate.js` 脚本可简化此自动化过程。

> [!WARNING]
> 该脚本会删除目标 API 管理服务中的开发人员门户内容。 如果你担心，请确保执行备份。

> [!NOTE]
> 如果使用自承载门户，并且具有显式定义的自定义存储帐户来承载媒体文件（例如，在 `config.design.json` 配置文件中定义 `blobStorageUrl` 设置），则需要使用原始 `scripts/migrate.js` [脚本](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js)。 原始脚本不适用于具有由 API 管理托管的媒体存储帐户的托管门户或自承载门户。 在这种情况下，请改用 `/scripts.v3` 文件夹中的脚本。

此脚本执行以下步骤：

1. 从源 API 管理服务捕获门户内容和媒体。
1. 从目标 API 管理服务删除门户内容和媒体。
1. 将门户内容和媒体上传到目标 API 管理服务。
1. 可选并且仅适用于托管门户 - 自动发布门户。

成功执行脚本后，目标 API 管理服务应包含与源服务相同的门户内容，你能够以管理员身份查看它。

* 如果使用的是托管门户，则可以将脚本设置为自动发布目标门户，以使迁移后的版本自动提供给访问者。 
* 如果使用的是自承载门户，则需要手动发布目标门户。 按照本教程中的发布和承载说明来[设置自承载开发人员门户](developer-portal-self-host.md)。

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
- [自承载开发人员门户](developer-portal-self-host.md)