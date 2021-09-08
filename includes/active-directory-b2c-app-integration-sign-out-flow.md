---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 7196d4596a3e67f1482fe2955a933fbf9efc8d47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227780"
---
注销流程涉及以下步骤：

1. 用户从应用中注销。
1. 应用清除其会话对象，并且身份验证库清除其令牌缓存。
1. 应用将用户定向到 Azure AD B2C 注销终结点，以终止 Azure AD B2C 会话。
1. 用户被重定向回应用。
