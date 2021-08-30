---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: f21c0a37d169885e60ae9dea34663dc0a86e161f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723285"
---
注销流程涉及以下步骤：

1. 用户从应用中注销。
1. 应用清除其会话对象，并且身份验证库清除其令牌缓存。
1. 应用将用户定向到 Azure AD B2C 注销终结点，以终止 Azure AD B2C 会话。
1. 用户被重定向回应用。
