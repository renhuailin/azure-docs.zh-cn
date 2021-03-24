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
ms.custom: azureday1
ms.openlocfilehash: 17931ee92072059d94cd950e9ce8ef6588f7e891
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905496"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>教程：在应用服务中启用身份验证和访问存储以及 Microsoft Graph

本教程介绍了常见的应用场景，在这些场景中你将了解如何：

- [为 Web 应用配置身份验证](scenario-secure-app-authentication-app-service.md)，并限制对组织中用户的访问。 请参见图中的 A。
- 使用托管标识为 Web 应用程序[安全访问 Azure 存储](scenario-secure-app-access-storage.md)。 请参见图中的 B。
- 使用托管标识[为登录的用户](scenario-secure-app-access-microsoft-graph-as-user.md)或[为 Web 应用程序](scenario-secure-app-access-microsoft-graph-as-app.md)访问 Microsoft Graph 中的数据。 请参见图中的 C。
- [清理为本教程创建的资源](scenario-secure-app-clean-up-resources.md)。

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="图显示了 Microsoft 标识平台中的应用程序方案。" border="false":::

首先，了解如何为 Web 应用启用身份验证。

> [!div class="nextstepaction"]
> [为 Web 应用配置身份验证](scenario-secure-app-authentication-app-service.md)
