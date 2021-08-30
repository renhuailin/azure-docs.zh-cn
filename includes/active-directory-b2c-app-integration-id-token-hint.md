---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f66eac86f4267a7b824eb551cdd877d3d34472a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723203"
---
## <a name="pass-an-id-token-hint"></a>传递 ID 令牌提示

依赖方应用可随附 OAuth2 授权请求发送入站 JSON Web 令牌 (JWT)。  入站令牌是有关用户或授权请求的提示。 Azure AD B2C 会验证令牌，然后提取声明。

要在身份验证请求中包含 ID 令牌提示，请执行以下操作：