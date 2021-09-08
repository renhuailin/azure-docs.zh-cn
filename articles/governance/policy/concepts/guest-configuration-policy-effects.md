---
title: 针对来宾配置的修正选项
description: 通过 Azure Policy 的来宾配置功能，可使用修正任务进行连续修正或控制。
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 6c16f4a2b20ea753b1ded4e8d389babc613b4b36
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868306"
---
# <a name="remediation-options-for-guest-configuration"></a>针对来宾配置的修正选项

在开始之前，最好先阅读[来宾配置](../concepts/guest-configuration.md)的概述页面。

> [!IMPORTANT]
> Azure 虚拟机要求具备来宾配置扩展。 若要在所有计算机上大规模部署此扩展，请分配以下策略计划：`Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> 若要使用应用配置的来宾配置包，需要安装 Azure VM 来宾配置扩展版本 1.29.24 或更高版本，或者 Arc 代理 1.10.0 或更高版本 。
>
> 使用 AuditIfNotExist 的自定义来宾配置策略定义已正式发布，但使用 DeployIfNotExists 的来宾配置定义目前以预览版提供  。

## <a name="how-remediation-set-is-managed-by-guest-configuration"></a>来宾配置如何管理修复 (Set)

来宾配置将策略效果 [DeployIfNotExists](../concepts/effects.md#deployifnotexists) 用于在计算机内部传递更改的定义。
设置策略分配的属性，以控制[评估](../concepts/effects.md#deployifnotexists-evaluation)是自动还是按需传递配置。

[提供本文档的视频演练](https://youtu.be/rjAk1eNmDLk)。

### <a name="guest-configuration-assignment-types"></a>来宾配置分配类型

创建来宾分配时，有 3 种可用的分配类型。
属性作为支持 DeployIfNotExists 的来宾配置定义的参数提供。

| 分配类型 | 行为 |
|-|-|
| 审核 | 报告计算机的状态，但不进行更改。 |
| ApplyandMonitor | 应用到计算机一次，然后监视更改。 如果配置偏移且其状态变为“不合规”，则不会自动更正，除非触发修正。 |
| ApplyandAutoCorrect | 应用到计算机。 如果它偏移，则计算机内的本地服务在下一次评估时进行更正。 |

在这三种分配类型中，将新的策略分配分配给现有计算机时，都会自动创建来宾分配来首先审核配置状态，提供信息以决定哪些计算机需要修正。

## <a name="remediation-on-demand-applyandmonitor"></a>按需修正 (ApplyAndMonitor)

默认情况下，来宾配置分配在“按需修正”方案中进行。 配置会被应用，随后可偏离合规性。 来宾分配的合规性状态为“合规”，除非在应用配置时发生错误，或在下一次评估期间计算机不再处于所需状态。 代理将报告状态“不合规”，并且不会自动修正。

若要启用此行为，请将来宾配置分配的 [assignmentType 属性](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)设置为“ApplyandMonitor”。 每次在计算机中处理分配时，对于每个资源，[Test](/powershell/scripting/dsc/resources/get-test-set#test) 方法都返回“true”，代理报告“合规”；如果方法返回“false”，则代理报告“不合规”。

## <a name="continuous-remediation-autocorrect"></a>连续修正 (AutoCorrect)

来宾配置支持“连续修正”这一概念。 如果计算机的配置偏移合规性，在下一次评估时会自动更正配置。 除非发生错误，否则计算机始终会将配置的状态报告为“合规”。 使用连续修正时，无法报告何时自动更正了偏移。

若要启用此行为，请将来宾配置分配的 [assignmentType 属性](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)设置为“ApplyandAutoCorrect”。 每次在计算机中处理分配时，对于每个资源，[Test](/powershell/scripting/dsc/resources/get-test-set#test) 方法都返回“false”，[Set](/powershell/scripting/dsc/resources/get-test-set#set) 方法会自动运行。

## <a name="disable-remediation"></a>禁用修正

`assignmentType` 属性设置为“Audit”时，代理仅对计算机执行审核，不会尝试修正不合规的配置。

### <a name="disable-remediation-of-custom-content"></a>禁用自定义内容修正

可向计算机添加名为 CustomGuestConfigurationSetPolicy 且值为 disable 的标记，替代自定义内容包的分配类型属性 。 添加标记只会对自定义内容包禁用修正，不会禁用对 Microsoft 提供的内置内容进行修正。

## <a name="azure-policy-enforcement"></a>Azure Policy 强制实施

Azure Policy 分配包括一个必需的属性[强制模式](../concepts/assignment-structure.md#enforcement-mode)，它用于确定新资源和现有资源的行为。
使用此属性可控制是否将配置自动应用到计算机。

默认情况下，强制属性设为“已启用”。 部署新计算机或更新计算机属性时，如果该计算机在 Azure Policy 分配范围内，并且在“来宾配置”类别中有策略定义，则 Azure Policy 会自动应用配置。 更新操作包括在 Azure 资源管理器中发生的操作，例如添加或更改标记，而对于虚拟机，则是重设大小或附加磁盘等更改。 如果 Azure 中的计算机资源发生更改时应修正配置，则将强制属性保持在启用状态。 只要计算机中发生的更改不会更改 Azure 资源管理器中的计算机资源，就不会触发自动修正。

如果强制属性设置为“已禁用”，则配置分配会审核计算机的状态，直到[修正任务](../how-to/remediate-resources.md)更改了该行为。 默认情况下，来宾配置定义将 [assignmentType 属性](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)从“Audit”更新为“ApplyandMonitor”，这样配置将应用一次，然后在触发修正之前不再应用。

## <a name="optional-remediate-all-existing-machines"></a>可选：修正所有现有计算机

如果通过 Azure 门户创建了 Azure Policy 分配，则“修正”选项卡上有一个“创建修正任务”复选框。 选中此框，在创建策略分配后，任何评估为“不合规”的资源都将通过修正任务自动更正。

此设置对来宾配置的影响是，你只需通过分配策略即可在多台计算机上部署配置。 而且，无需为不合规的计算机手动运行修正任务。

## <a name="manually-trigger-remediation-outside-of-azure-policy"></a>在 Azure Policy 之外手动触发修正

还可通过更新来宾分配资源来协调 Azure Policy 体验之外的修正，即使更新未对资源属性进行更改也是如此。

创建来宾配置分配时，[complianceStatus 属性](/rest/api/guestconfiguration/guest-configuration-assignments/get#compliancestatus)设置为“挂起”。
计算机中的来宾配置服务通过[来宾配置扩展](../../../virtual-machines/extensions/guest-configuration.md)传递到 Azure 虚拟机并随已启用 Arc 的服务器提供，该服务每 5 分钟请求一次分配列表。
如果来宾配置分配同时具有两项要求，`complianceStatus` 为“挂起”，`configurationMode` 为“ApplyandMonitor”或“ApplyandAutoCorrect”，则计算机中的服务会应用该配置。 应用配置后，在[下一个时间间隔](./guest-configuration.md#validation-frequency)，配置模式将指示该行为是只报告合规性状态并允许偏移还是自动进行更正。

## <a name="understanding-combinations-of-settings"></a>了解设置的组合

|~| 审核 | ApplyandMonitor | ApplyandAutoCorrect |
|-|-|-|-|
| 已启用强制 | 仅报告状态 | 配置应用于 VM 创建，并在更新时重新应用，但其他方面允许偏移 | 配置应用于 VM 创建并在更新时重新应用，如果发生偏移，则在下一个时间间隔更正 |
| 已禁用强制 | 仅报告状态 | 已应用配置但允许偏移 | 配置应用于 VM 创建或更新，如果发生偏移，则在下一个时间间隔更正 |

## <a name="next-steps"></a>后续步骤

- 阅读[来宾配置概述](./guest-configuration.md)。
- 设置名为[开发环境](../how-to/guest-configuration-create-setup.md)的自定义来宾配置包。
- 为来宾配置[创建包项目](../how-to/guest-configuration-create.md)。
- 从开发环境[测试包项目](../how-to/guest-configuration-create-test.md)。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](../how-to/guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)。
