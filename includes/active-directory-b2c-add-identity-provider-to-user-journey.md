---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98674214"
---
## <a name="add-a-user-journey"></a>添加用户旅程 

此时，标识提供者已设置，但还不能在任何登录页中使用。 如果你没有自己的自定义用户旅程，请创建现有模板用户旅程的副本，否则，请继续执行下一步。 

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 对用户旅程的 ID 进行重命名。 例如，`Id="CustomSignUpSignIn"`。

## <a name="add-the-identity-provider-to-a-user-journey"></a>将标识提供者添加到用户旅程 

目前你已拥有用户旅程，请将新的标识提供者添加到用户旅程。 首先添加一个“登录”按钮，然后将该按钮链接到某个操作。 该操作是你之前创建的技术配置文件。

1. 在用户旅程中，查找包含 `Type="CombinedSignInAndSignUp"` 或 `Type="ClaimsProviderSelection"` 的业务流程步骤元素。 这通常是第一个业务流程步骤。 ClaimsProviderSelections 元素包含用户可以用来登录的标识提供者列表。 元素的顺序将决定向用户显示的登录按钮的顺序。 添加 ClaimsProviderSelection XML 元素。 将 TargetClaimsExchangeId 的值设置为易记名称。

1. 在下一个业务流程步骤中，添加 ClaimsExchange 元素。 将 ID 设置为目标声明交换 ID 的值。将 TechnicalProfileReferenceId 的值更新为之前创建的技术配置文件的 ID 。

下面的 XML 演示了使用标识提供者进行用户旅程的前两个业务流程步骤：