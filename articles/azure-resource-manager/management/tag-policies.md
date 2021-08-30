---
title: 标记资源的策略定义
description: 介绍可分配以确保标记合规性的 Azure Policy 定义。
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f7af799423b0aceac66dfa97e92c92d25c3916db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456250"
---
# <a name="assign-policy-definitions-for-tag-compliance"></a>为标记合规性分配策略定义

使用 [Azure Policy](../../governance/policy/overview.md) 强制实施标记规则和约定。 通过创建策略，可以避免将没有组织所需标记的资源部署到订阅。 不要手动应用标记或搜索不符合的资源，请创建一个策略，用于在部署期间自动应用所需标记。 现在，还可以通过新的 [Modify](../../governance/policy/concepts/effects.md#modify) 效果和[修正任务](../../governance/policy/how-to/remediate-resources.md)将标记应用于现有资源。 以下部分展示标记的策略定义示例。

## <a name="policy-definitions"></a>策略定义

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>后续步骤

* 若要了解如何标记资源，请参阅[使用标记来组织 Azure 资源](tag-resources.md)。
* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
