---
title: 了解 Azure Policy 中的范围
description: 说明 Azure 资源管理器中范围的概念，以及如何将其应用于 Azure Policy，从而控制哪些资源由 Azure Policy 评估。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: b83acebe3f24b7dac1c506a598c641035132fb30
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321948"
---
# <a name="understand-scope-in-azure-policy"></a>了解 Azure Policy 中的范围

有许多设置用于确定哪些资源可以接受评估以及哪些资源由 Azure Policy 评估。 这些控制措施的首要概念是范围。 Azure Policy 中的范围基于 Azure 资源管理器中范围的工作方式。 有关综合概述，请参阅 [Azure 资源管理器中的范围](../../../azure-resource-manager/management/overview.md#understand-scope)。
本文介绍了 Azure Policy 中范围的重要性，以及它的相关对象和属性。

## <a name="definition-location"></a>定义位置

Azure Policy 使用第一个实例范围的时间是在创建策略定义的时候。 该定义可以保存在管理组或订阅中。 该位置确定计划或策略可以分配到的范围。 资源必须位于作为分配目标的定义位置的资源层次结构之内。

如果定义位置是：

- **订阅** - 策略定义只能分配到该订阅内的资源。
- **管理组** - 策略定义只能分配到子管理组和子订阅内的资源。 如果你计划将此策略定义应用于多个订阅，该位置必须是包含每个订阅的管理组。

该位置应该是由你想要对其使用策略定义（如果存在）的所有资源共享的资源容器。 此资源容器通常是位于根管理组附近的某个管理组。

## <a name="assignment-scopes"></a>分配范围

一个分配具有多个用于设置范围的属性。 对这些属性的使用决定了要评估哪个 Azure Policy 资源以及哪些资源会计入符合性。 这些属性映射到以下概念：

- 包含 - 应该按定义来评估资源层次结构或单个资源的符合性。 分配对象的 `properties.scope` 属性确定针对符合性要包含和评估什么内容。 有关详细信息，请参阅[分配定义](./assignment-structure.md)。

- 排除 - 不应该按定义来评估资源层次结构或单个资源的符合性。 某个分配对象的 `properties.notScopes` 数组属性确定要排除什么内容。 这些范围内的资源不会被评估，也不会包含在符合性计算中。 有关详细信息，请参阅[分配定义 - 排除的范围](./assignment-structure.md#excluded-scopes)。

除了策略分配的属性外，还有一个[策略豁免](./exemption-structure.md)对象。 豁免提供一种方法来识别某个分配中不进行评估的部分，从而增强范围描述。

- 豁免（免费预览版功能）- 应该按定义来评估资源层次结构或单个资源的符合性，但由于某种原因（如通过其他方法得到豁免或缓解）而不会对其进行评估。 处于这种状态的资源在符合性报告中会显示为“已豁免”，以便可对这些资源进行跟踪。 豁免对象在资源层次结构或单个资源上创建为子对象，从而确定了豁免的范围。 资源层次结构或单个资源可以从多个分配豁免。 通过使用 `expiresOn` 属性，可以将豁免配置为按计划过期。 有关详细信息，请参阅[豁免定义](./exemption-structure.md)。

  > [!NOTE]
  > 由于为资源层次结构或单个资源授予豁免会产生影响，因此豁免还具有更多的安全措施。 除了需要对资源层次结构或单个资源执行 `Microsoft.Authorization/policyExemptions/write` 操作外，豁免的创建者还必须在目标分配上使用 `exempt/Action` 谓词。

## <a name="scope-comparison"></a>范围对比

下表是范围选项的对比：

| | 包含 | 排除 (notScopes) | 例外 |
|---|:---:|:---:|:---:|
|资源接受评估 | &#10004; | - | - |
|资源管理器对象 | - | - | &#10004; |
|需要修改策略分配对象 | &#10004; | &#10004; | - |

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。