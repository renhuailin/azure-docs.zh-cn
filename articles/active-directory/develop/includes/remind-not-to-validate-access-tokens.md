---
title: 请勿验证 Microsoft Graph 的访问令牌
description: 包括文件警告，指出 Microsoft Graph 的访问令牌应被视为不透明，永远不应由客户代码进行验证。 只有 Microsoft Graph 才能验证 Microsoft Graph 访问令牌。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ab38a17f96bf10d5ae6745ede5c89c90eb47fda7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432046"
---
> [!WARNING]
> 请勿尝试在代码中验证或读取你未拥有的任何 API 的令牌，包括此示例中的令牌。  Microsoft 服务的令牌可以使用将不会作为 JWT 进行验证的特殊格式，还可以针对使用者（Microsoft 帐户）用户加密。 虽然可以通过读取令牌的操作进行调试和学习，但请不要在代码中依赖此操作，也不要假定不是你控制的 API 的令牌的相关具体信息。
