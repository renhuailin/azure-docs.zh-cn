---
title: Azure Active Directory B2C 中的子旅程 | Microsoft Docs
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97386861"
---
# <a name="sub-journeys"></a>子旅程

子旅程可用于组织和简化用户旅程中的业务流程步骤流。 [用户旅程](userjourneys.md)指定策略允许信赖方应用程序为用户获取所需声明的显式路径。 用户通过这些路径检索要提供给信赖方的声明。 换言之，用户旅程定义最终用户在 Azure AD B2C 标识体验框架处理请求时所经历的业务逻辑。 用户旅程表示为成功事务必须遵循的业务流程序列。 业务流程步骤的 [ClaimsExchange](userjourneys.md#claimsexchanges) 元素绑定到执行的单个[技术配置文件](technicalprofiles.md)。

子旅程是业务流程步骤的分组，可以在用户旅程中的任何时间点调用。 你可以使用子旅程来创建可重用的步骤序列，或实施分支来更好地表示业务逻辑。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>用户旅程分支

子旅程的行为类似于[用户旅程](userjourneys.md)，它们都可以表示为事务获得成功必须遵循的业务流程序列。 用户旅程可以自行调用，并且需要执行 SendClaims 步骤。 子旅程是用户旅程的组件，不能单独调用，并且始终从用户旅程中调用。

分支的关键部分是允许在用户旅程中进行更好的业务逻辑处理。 常见的业务流程步骤分组为单独的部分，以便分别调用。 子旅程可简化将多个业务流程步骤（具有相同的前提条件）耦合在一起的旅程。 子旅程仅从用户旅程中调用，它不应调用另一个子旅程。

有两种类型的子旅程：

- **Call** - 将控制权返回给调用方。 执行子旅程后，将控制权返回给用户旅程中当前正在执行的业务流程步骤。
- 转移 - 将控制权转移给子旅程（不可逆的分支）。 子旅程必须具有 SendClaims 步骤，才能将声明返回给信赖方应用。

## <a name="example-scenarios"></a>示例方案

### <a name="call-sub-journey"></a>“调用”子旅程

“调用”子旅程在以下情况下非常有用：

- 年龄限制：对于年龄限制，用户旅程中有许多共享组件。 分支允许将公共元素编译为可共享的组件。  
- 家长同意：分支允许我们访问未成年人执行的用户旅程中的声明，以及在发现用户需要同意后能够分支到家长同意用户旅程中，从而在家长同意设计中提供了便利。 
- 注册以登录：请考虑这样一种情况：用户已存在于目录中，但可能忘记了他们实际上已创建了帐户。 在这种情况下，可以让策略为用户执行切换（从注册流切换到登录流），而不是告诉该用户他们输入的凭据已经存在，并强制该用户重新开始旅程。  

### <a name="transfer-sub-journey"></a>“转移”子旅程

“转移”子旅程在以下情况下非常有用：

- 显示块页。
- A/B 测试，通过将请求路由到子旅程来执行和颁发令牌。

## <a name="adding-a-subjourneys-element"></a>添加 SubJourneys 元素

下面是 `Call` 类型的 `SubJourney` 元素的一个示例，它将控制权返还给用户旅程。

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

下面是 `Transfer` 类型的 `SubJourney` 元素的一个示例，它将一个令牌返回给信赖方应用程序。

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

### <a name="invoke-a-sub-journey-step"></a>调用子旅程步骤

`InvokeSubJourney` 类型的新业务流程步骤用于执行子旅程。 以下示例显示了此业务流程步骤的所有执行元素。

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> 一个子旅程不应调用另一个子旅程。

## <a name="components"></a>组件

若要定义策略支持的子旅程，请在策略文件的最上层元素下添加 SubJourneys 元素。

SubJourneys 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 一个定义了完整用户流所需的所有构造的子旅程。 |

SubJourneys 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 子旅程标识符，用户旅程可使用它在策略中引用子旅程。 [Candidate](userjourneys.md#journeylist) 元素的 **SubJourneyReferenceId** 元素指向此属性。 |
| 类型 | 是 | 可能的值：`Call` 或 `Transfer`。 有关详细信息，请参阅[用户旅程分支](#user-journey-branching)|

SubJourney 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 成功事务必须遵循的业务流程序列。 每个用户旅程都包含按顺序执行的业务流程步骤的有序列表。 如果任何步骤失败，则事务将失败。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

有关业务流程步骤元素的完整列表，请参阅 [UserJourneys](userjourneys.md)。

## <a name="next-steps"></a>后续步骤

了解 [UserJourneys](userjourneys.md)