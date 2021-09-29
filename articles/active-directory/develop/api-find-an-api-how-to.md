---
title: 查找自定义开发的应用的 API | Azure
description: 如何配置访问自定义开发的 Azure AD 应用程序中的特定 API 所需的权限
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2d5c7f6e603b79b87297b0bbadb3d210ceaf0484
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154653"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>如何为自定义开发的应用程序查找所需的特定 API

访问 API 需要配置访问作用域和访问角色。 如果想要将资源应用程序 Web API 公开至客户端应用程序，请为 API 配置访问作用域和访问角色。 如果想要让客户端应用程序访问 Web API，请配置权限以访问应用注册中的 API。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>将资源应用程序配置为公开 Web API

当 Web API 公开后，将权限添加到应用注册时，API 会显示在“选择 API”列表中。 若要添加访问范围，请按照[配置应用程序以公开 Web API](quickstart-configure-app-expose-web-apis.md) 中列出的步骤进行操作。

## <a name="configuring-a-client-application-to-access-web-apis"></a>将客户端应用程序配置为访问 Web API

在将权限添加到应用注册时，可 **添加 API 访问** 到已公开的 Web API。 若要访问 Web API，请按照[配置客户端应用程序以访问 Web API](quickstart-configure-app-access-web-apis.md) 中列出的步骤进行操作。

## <a name="next-steps"></a>后续步骤

- [了解 Azure Active Directory 应用程序清单](./reference-app-manifest.md)
