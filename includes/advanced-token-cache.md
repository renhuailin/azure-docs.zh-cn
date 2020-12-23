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
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2020
ms.locfileid: "94627995"
---
你可以使用 MSAL 的令牌缓存实现，以允许后台应用程序、Api 和服务使用访问令牌缓存，以便在用户缺勤时继续代表用户执行操作。 如果后台应用和服务在用户退出前端 web 应用后需要继续代表用户工作，则这样做特别有用。

目前，大多数后台进程都在需要使用用户的数据时使用 [应用程序权限](/graph/auth/auth-concepts#microsoft-graph-permissions) ，而无需对用户进行身份验证或身份验证。 由于应用程序权限通常需要管理员许可，这需要提升权限，因此当开发人员不打算获得超过用户最初同意的应用程序的权限时，就会遇到不必要的问题。

GitHub 上的此代码示例演示了如何通过从后台应用访问 MSAL 的令牌缓存来避免这种不必要的摩擦：

 [从后台应用、Api 和服务访问登录用户的令牌缓存](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)