---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 18120c126fafd03b6bb91901b33f8932644079c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122868243"
---
为注册的 Web 应用创建客户端密码。 Web 应用在请求令牌时使用客户端密码来证明其身份。

1. 在“管理”下，选择“证书和机密”。 
1. 选择“新建客户端机密”。
1. 在“说明”框中输入对客户端密码的说明（如 clientsecret1）。
1. 在“过期时间”下，选择机密持续生效的时间，然后选择“添加”。
1. 记下机密的“值”。 将该值用于后面的一个步骤中的配置。