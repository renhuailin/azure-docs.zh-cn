---
title: Azure Active Directory B2C 中的子旅程 |Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的子旅程元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97386861"
---
# <a name="sub-journeys"></a>Sub 旅程

可以使用 Sub 旅程来组织和简化用户旅程中的业务流程步骤流。 [用户旅程](userjourneys.md)指定策略允许信赖方应用程序为用户获取所需声明的显式路径。 用户通过这些路径检索要提供给信赖方的声明。 换言之，用户旅程定义最终用户在 Azure AD B2C 标识体验框架处理请求时所经历的业务逻辑。 用户旅程表示为成功事务必须遵循的业务流程序列。 业务流程步骤的 [ClaimsExchange](userjourneys.md#claimsexchanges) 元素绑定到执行的单个[技术配置文件](technicalprofiles.md)。

Sub 旅程是可在用户旅程中的任何时刻调用的业务流程步骤的分组。 可以使用 sub 旅程创建可重用的步骤序列，或实现分支以更好地表示业务逻辑。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>用户旅程分支

Sub 旅程的行为类似于 [用户旅程](userjourneys.md)，因为这两种方法都表示为成功完成事务所必须遵循的业务流程序列。 用户旅程可以自行调用，并且需要执行 SendClaims 步骤。 Sub 旅程是 user 旅程的组成部分，不能独立调用，始终在用户旅程中调用。

分支的关键部分是允许在用户旅程中进行更好的业务逻辑处理。 常见的业务流程步骤分组为单独的部分，以便分别调用。 Sub 旅程可简化一项旅程，其中，多个业务流程步骤结合在一起 () 前提条件相同。 仅在用户旅程中调用 sub 旅程，而不应调用另一个 sub 旅程。

有两种类型的 sub 旅程：

- **Call** - 将控制权返回给调用方。 Sub 旅程执行，然后控制权返回到当前在用户旅程中执行的业务流程步骤。
- **传输** -将控制转移到 (不可逆分支) 的 sub 旅程。 Sub 旅程必须具有 SendClaims 步骤，将声明返回给信赖方应用程序。

## <a name="example-scenarios"></a>示例方案

### <a name="call-sub-journey"></a>调用 sub 旅程

对于以下情况，调用 sub 旅程非常有用：

- 年龄限制：对于年龄限制，用户旅程中有许多共享组件。 分支允许将公共元素编译为可共享的组件。  
- 家长同意：分支允许我们访问未成年人执行的用户旅程中的声明，以及在发现用户需要同意后能够分支到家长同意用户旅程中，从而在家长同意设计中提供了便利。 
- 注册以登录：请考虑这样一种情况：用户已存在于目录中，但可能忘记了他们实际上已创建了帐户。 在这种情况下，可能需要这样做，而不是告诉用户他们所输入的凭据已经存在，并强制用户重新启动旅程，以便策略能够从注册流到该用户的登录流执行切换。  

### <a name="transfer-sub-journey"></a>传输 sub 旅程

传输 sub 旅程在下列情况下非常有用：

- 显示块页。
- A/B 测试，通过将请求路由到 sub 旅程来执行和颁发令牌。

## <a name="adding-a-subjourneys-element"></a>添加 SubJourneys 元素

下面是类型为的元素的一个示例 `SubJourney` `Call` ，它将控制权返回给用户旅程。

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

下面是类型为的元素的一个示例 `SubJourney` `Transfer` ，它将一个令牌返回给信赖方应用程序。

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>调用 sub 旅程步骤

类型为的新业务流程步骤 `InvokeSubJourney` 用于执行 sub 旅程。 下面是一个示例，演示了此业务流程步骤的所有执行元素。

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Sub 旅程不应调用另一个 sub 旅程。

## <a name="components"></a>组件

若要定义策略支持的子旅程，请在策略文件的顶级元素下添加一个 **SubJourneys** 元素。

SubJourneys 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 定义完整用户流所需的所有构造的一种 sub 旅程。 |

SubJourneys 元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | Sub 旅程标识符，用户旅程可使用该标识符引用策略中的 sub 旅程。 [Candidate](userjourneys.md#journeylist) 元素的 **SubJourneyReferenceId** 元素指向此属性。 |
| 类型 | 是 | 可能的值：`Call` 或 `Transfer`。 有关详细信息，请参阅[用户旅程分支](#user-journey-branching)|

SubJourney 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 成功事务必须遵循的业务流程序列。 每个用户旅程都包含按顺序执行的业务流程步骤的有序列表。 如果任何步骤失败，则事务将失败。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

有关业务流程步骤元素的完整列表，请参阅 [UserJourneys](userjourneys.md)。

## <a name="next-steps"></a>后续步骤

了解 [UserJourneys](userjourneys.md)