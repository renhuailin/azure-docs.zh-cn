---
title: 准备移动应用 - 为生产调用 Web API | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用。 （准备用于生产的应用。）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7756029d4b3ed43f55c86fa12128e1943c99b6bb
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778384"
---
# <a name="prepare-mobile-apps-for-production"></a>准备用于生产的移动应用

本文详细介绍了如何在将移动应用移到生产环境之前提高移动应用的质量和可靠性。

## <a name="handle-errors"></a>处理错误

在为生产准备移动应用时，可能会出现几个错误情况。 要处理的主要情况是无提示失败和回退到交互。 应考虑的其他情况包括无网络情况、服务中断、管理员同意的要求以及其他特定于场景的情况。

对于每个 Microsoft 身份验证库 (MSAL) 类型，可以找到说明如何处理错误情况的示例代码和 wiki 内容：

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

若要尝试其他示例，请参阅[移动公共客户端应用程序](sample-v2-code.md#mobile)。