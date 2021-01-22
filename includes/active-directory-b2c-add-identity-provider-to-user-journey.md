---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674214"
---
## <a name="add-a-user-journey"></a>添加用户旅程 

此时，标识提供者已设置，但在任何登录页中均不可用。 如果你没有自己的自定义用户旅程，请创建现有模板用户旅程的副本，否则，请继续执行下一步。 

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 Id。 例如，`Id="CustomSignUpSignIn"`。

## <a name="add-the-identity-provider-to-a-user-journey"></a>将标识提供者添加到用户旅程 

现在，你已拥有用户旅程，请将新的标识提供者添加到用户旅程。 首先添加一个 "登录" 按钮，然后将该按钮链接到某个操作。 操作是您之前创建的技术配置文件。

1. 查找包含 `Type="CombinedSignInAndSignUp"` 或用户旅程中的业务流程步骤元素 `Type="ClaimsProviderSelection"` 。 它通常是第一个业务流程步骤。 **ClaimsProviderSelections** 元素包含用户可以用来登录的标识提供者的列表。 元素的顺序控制向用户显示的登录按钮的顺序。 添加 **claimsexchange** XML 元素。 将 **TargetClaimsExchangeId** 的值设置为友好名称。

1. 在下一个业务流程步骤中，添加一个 **ClaimsExchange** 元素。 将 **Id** 设置为目标声明交换 Id 的值。将 **TechnicalProfileReferenceId** 的值更新为之前创建的技术配置文件的 Id。

下面的 XML 演示用户使用标识提供程序进行旅程的前两个业务流程步骤：