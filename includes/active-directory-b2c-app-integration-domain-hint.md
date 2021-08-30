---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: bade30eb42235e65170fc59ad77fa2f4ce1114c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723207"
---
## <a name="preselect-an-identity-provider"></a>预先选择标识提供程序

如果已将应用程序的登录旅程配置为包括社交帐户（如 Facebook、LinkedIn 或 Google），则可以指定 `domain_hint` 参数。 此查询参数向 Azure AD B2C 提供有关应该用于登录的社交标识提供者的提示。 例如，如果应用程序指定 `domain_hint=facebook.com`，登录流会直接转到 Facebook 登录页。 

要将用户重定向到外部标识提供程序，请执行以下操作：
