---
title: include 文件
description: include 文件
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94627995"
---
你可以使用 MSAL 的令牌缓存实现来允许后台应用、API 和服务使用访问令牌缓存，以便在用户不在时代表用户继续执行操作。 如果后台应用和服务需要在用户退出前端 Web 应用后继续代表用户工作，则这样做特别有用。

如今，大多数后台进程都在需要使用用户的数据时使用[应用程序权限](/graph/auth/auth-concepts#microsoft-graph-permissions)，而无需用户在场进行身份验证或重新进行身份验证。 由于应用程序权限通常需要管理员同意，这需要特权提升，因此会遇到不必要的摩擦，因为开发人员不打算获得针对其应用的、超出用户最初同意的权限的权限。

GitHub 上的此代码示例演示了如何通过从后台应用访问 MSAL 的令牌缓存来避免这种不必要的摩擦：

 [从后台应用、API 和服务访问已登录用户的令牌缓存](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)