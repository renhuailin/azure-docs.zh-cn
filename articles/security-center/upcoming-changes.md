---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/19/2021
ms.author: memildin
ms.openlocfilehash: 0e9988aa7779d85714d6e1562af78c3a5bf0e1ff
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444687"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

| 计划的更改       | 预计更改日期 |
|----------------------|---------------------------|
| [正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| 2021 年 8 月|
| [将某些警报类型的前缀从“ARM_”更改为“VM_”](#changing-prefix-of-some-alert-types-from-arm_-to-vm_)                                          | 2021 年 10 月|
| [关于管理 Endpoint Protection 解决方案的更改建议](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | 2021 年第 4 季度    |
| [针对用于对 SQL 数据库中的敏感数据进行分类的建议进行了增强](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | 2022 年第 1 季度    |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013

**预计更改日期：** 2021 年 8 月

将从安全中心的监管合规性仪表板中删除 ISO 27001 的旧版实现。 如果要跟踪 ISO 27001 与安全中心的合规性，请针对所有相关管理组或订阅加入新的 ISO 27001:2013 标准，很快将会从仪表板中删除当前的旧版 ISO 27001。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="安全中心的监管合规性仪表板，其中显示了有关删除 ISO 27001 的旧版实现的消息。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changing-prefix-of-some-alert-types-from-arm_-to-vm_"></a>将某些警报类型的前缀从“ARM_”更改为“VM_” 

**预计更改日期：** 2021 年 10 月

2021 年 7 月，我们发布了[适用于资源管理器的 Azure Defender 的警报的逻辑重组](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) 

在对某些 Azure Defender 计划进行逻辑重组的过程中，我们已将 21 条警报从[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 移至[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。

我们现在正在计划更新这些警报的前缀，以与此重新分配相符。 我们会将“ARM_”替换为“VM_”，如下表所示。

| 当前名称                                   | 更改后的名称                             |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |

详细了解[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 计划。


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>关于管理 Endpoint Protection 解决方案的更改建议

**预计更改日期：** 2021 年第 4 季度

2021 年 8 月，我们就如何在计算机上部署和维护 Endpoint Protection 解决方案，增加了两项新预览建议。 有关完整的详细信息，请参阅[发行说明](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)。

正式发布这些建议时，它们将替换以下现有建议：

- **应在计算机上安装 Endpoint Protection** 将替换：
    - 在虚拟机上安装 Endpoint Protection 解决方案
    - 在计算机上安装 Endpoint Protection 解决方案 

- **应在计算机上解决 Endpoint protection 运行状况问题** 将替换具有相同名称的现有建议。 这两个建议有不同的评估密钥：
    - 预览版建议的评估密钥：37a3689a-818e-4a0e-82ac-b1392b9bb000
    - GA 建议的评估密钥：3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

了解详细信息：
- [安全中心的受支持 Endpoint Protection 解决方案](security-center-services.md#endpoint-supported)
- [这些建议如何评估部署的解决方案的状态](security-center-endpoint-protection.md)

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>针对用于对 SQL 数据库中的敏感数据进行分类的建议进行了增强

**预计更改日期：** 2022 年第 1 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前 ID 为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。


## <a name="next-steps"></a>后续步骤

有关安全中心的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)