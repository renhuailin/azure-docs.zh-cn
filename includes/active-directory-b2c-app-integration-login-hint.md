---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 2bab69917caf103cd4e2f0b10e28b46acd8e48c3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220298"
---
## <a name="prepopulate-the-sign-in-name"></a>预填充登录名

在登录用户旅程中，你的应用可能会针对特定用户。 当应用针对用户时，它可以在授权请求中使用用户登录名指定 `login_hint` 查询参数。 Azure AD B2C 自动填充登录名，用户只需要提供密码。 

若要预填充登录名，请执行下列操作：