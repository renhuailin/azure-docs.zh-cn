---
title: 教程 - 在 Azure 应用服务上构建安全的 Web 应用 | Azure
description: 在本教程中，了解如何使用 Azure 应用服务构建 Web 应用、启用身份验证、调用 Azure 存储和调用 Microsoft Graph。
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef7782c68746d4c20df5a9ae5e27ffca3e60efbe
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024120"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>教程：在应用服务中启用身份验证和访问存储以及 Microsoft Graph

本教程介绍了常见的应用场景，你将了解如何：

- [为 Web 应用配置身份验证](scenario-secure-app-authentication-app-service.md)，并限制对组织中用户的访问。 查看图中的 A。
- 使用托管标识针对 Web 应用程序[安全地访问 Azure 存储](scenario-secure-app-access-storage.md)。 查看图中的 B。
- 使用托管标识针对[已登录用户](scenario-secure-app-access-microsoft-graph-as-user.md)或 [Web 应用程序](scenario-secure-app-access-microsoft-graph-as-app.md)访问 Microsoft Graph 中的数据。 查看图中的 C。
- [清理为本教程创建的资源](scenario-secure-app-clean-up-resources.md)。

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="关系图显示 Microsoft 标识平台中的应用场景。" border="false":::

首先，了解如何为 Web 应用启用身份验证。

> [!div class="nextstepaction"]
> [为 Web 应用配置身份验证](scenario-secure-app-authentication-app-service.md)
