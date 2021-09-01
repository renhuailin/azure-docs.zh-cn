---
title: 'Azure AD Connect 单一对象同步 '
description: 了解如何将一个对象从 Active Directory 同步到 Azure AD 以进行故障排除。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/24/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dec171c299bdd8ea6c5989794539b6c79e7d3fe
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229682"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect 单一对象同步 

Azure AD Connect 单一对象同步工具是一个 PowerShell cmdlet，可用于将单个对象从 Active Directory 同步到 Azure Active Directory。 生成的报表可用于调查和排查每个对象的同步问题。 

> [!NOTE]
> 该工具支持从 Active Directory 到 Azure Active Directory 的同步。 它不支持从 Azure Active Directory 到 Active Directory 的同步。 
>
> 该工具支持同步对象修改添加和更新。 它不支持同步对象修改删除。 

## <a name="how-it-works"></a>工作原理
单一对象同步工具需使用 Active Directory 可分辨名称作为输入，来查找用于导入的源连接器和分区。 它会将更改导出到 Azure Active Directory。 该工具生成类似于 **provisioningObjectSummary** 资源类型的 JSON 输出。 

单一对象同步工具执行以下步骤： 

 1. 确定对象的（源）域（Active Directory 连接器和分区）是否在同步范围内。 
 2. 确定对象的（目标）域（Azure Active Directory 连接器和分区）是否在同步范围内。 
 3. 确定对象的组织单位是否在同步范围内。 
 4. 确定是否可以使用连接器帐户凭据访问对象。 
 5. 确定对象的类型是否在同步范围内。 
 6. 确定当启用了组筛选时对象是否在同步范围内。 
 7. 将对象从 Active Directory 导入到 Active Directory 连接器空间。 
 8. 将对象从 Azure Active Directory 导入到 Azure Active Directory 连接器空间。 
 9. 同步 Active Directory 连接器空间中的对象。 
 10. 将对象从 Azure Active Directory 连接器空间导出到 Azure Active Directory。 

除 JSON 输出外，该工具还会生成 HTML 报表，其中包含同步操作的所有详细信息。 该 HTML 报表位于 **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult-<date>.htm** 中。 此 HTML 报表可以与支持团队共享，以便根据需要进行进一步的故障排除。 

HTML 报表包含以下内容： 

|选项卡|说明|
|-----|-----|
|步骤|概述同步对象所要执行的步骤。 每个步骤包含故障排除详细信息。 导入、同步和导出步骤包含其他特性信息，如名称、是否为多值、类型、值、值添加、值删除、操作、同步规则、映射类型和数据源。| 
|故障排除和建议|提供错误代码和原因。 仅当发生失败时，才提供错误信息。| 
|修改后的属性|显示旧值和新值。 如果没有旧值或者已删除新值，则该单元格为空。 对于多值特性，它会显示计数。 特性名称是“步骤”选项卡的链接：“将对象从 Azure Active Directory 连接器空间导出到 Azure Active Directory”；“特性信息”，其中包含名称、是否为多值、类型、值、值添加、值删除、操作、同步规则、映射类型和数据源等其他详细信息。| 
|总结|概述发生的情况，并提供源系统和目标系统中对象的标识符。| 

## <a name="prerequisites"></a>先决条件 

若要使用单一对象同步工具，需要使用以下工具：  
 - Azure AD Connect 2021 年 3 月版本 ([1.6.4.0](reference-connect-version-history.md#1640)) 或更高版本。
 -  [PowerShell 5.0](/powershell/scripting/windows-powershell/whats-new/what-s-new-in-windows-powershell-50?view=powershell-7.1)

### <a name="run-the-single-object-sync-tool"></a>运行单一对象同步工具 

若要运行单一对象同步工具，请执行以下步骤： 

 1. 使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。 

 2. 将[执行策略](/powershell/module/microsoft.powershell.security/set-executionpolicy)设置为 RemoteSigned 或 Unrestricted。 

 3. 确认没有同步操作正在运行后，禁用同步计划程序。 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. 导入 AdSync 诊断模块 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. 调用单一对象同步 cmdlet。 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. 重新启用同步计划程序。 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|单一对象同步输入参数|说明| 
|-----|----|
|DistinguishedName|这是一个必需的字符串参数。 </br></br>这是需要同步和故障排除的 Active Directory 对象可分辨名称。| 
|StagingMode|这是可选的开关参数。</br></br>此参数可用于防止将更改导出到 Azure Active Directory。</br></br>**注意**：该 cmdlet 将提交同步操作。 </br></br>**注意**：Azure AD Connect 暂存服务器不会将更改导出到 Azure Active Directory。|
|NoHtmlReport|这是可选的开关参数。</br></br>此参数可用于防止生成 HTML 报表。 

## <a name="single-object-sync-throttling"></a>单一对象同步限制 

单一对象同步工具用于调查和排查每个对象的同步问题。 它并 **不** 用于取代计划程序运行的同步周期。 从 Azure Active Directory 导入以及导出到 Azure Active Directory 会受到限制。 如果达到限制，请在 5 分钟后重试。 

## <a name="next-steps"></a>后续步骤
- [排查对象同步问题](tshoot-connect-objectsync.md)
- [排查对象不同步的问题](tshoot-connect-object-not-syncing.md)
- [Azure AD Connect 对象和属性的端到端故障排除](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)