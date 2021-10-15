---
title: 条件访问策略中用作条件的设备筛选器 - Azure Active Directory
description: 在条件访问中使用设备筛选器来增强安全态势
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c167752881d1a6be7b51db16e2556ac3c781bd0
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455449"
---
# <a name="conditional-access-filters-for-devices-preview"></a>条件访问：设备筛选器（预览版）

创建条件访问策略时，管理员需要能够将其环境中的特定设备指定为目标或者排除这些设备。 预览版设备条件筛选器为管理员提供了这种能力。 现在，你可以在条件访问策略中使用[筛选器支持的运算符和设备属性](#supported-operators-and-device-properties-for-filters)和其他可用分配条件，将特定的设备指定为目标。

:::image type="content" source="media/concept-condition-filters-for-devices/create-filter-for-devices-condition.png" alt-text="在条件访问策略条件中创建设备筛选器":::

> [!IMPORTANT]
> 设备筛选器目前以公共预览版提供。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="common-scenarios"></a>常见方案

现在，组织可以使用设备筛选器条件实现多种方案。 下面是一些核心方案，以及演示如何使用此新条件的示例。

- 将对特权资源（如 Microsoft Azure 管理）的访问限定为只能由特权用户通过[特权或安全管理工作站](/security/compass/privileged-access-devices)进行。 对于此方案，组织需创建两个条件访问策略：
   - 策略 1：拥有“全局管理员”目录角色的所有用户可以访问 Microsoft Azure 管理云应用，并可为“访问控制”授予访问权限，但需要执行多重身份验证，并要求设备标记为合规。
   - 策略 2：拥有“全局管理员”目录角色的所有用户可以访问 Microsoft Azure 管理云应用（但不包括使用规则表达式“device.extensionAttribute1 equals SAW”的设备筛选器），并可以阻止“访问控制”。
- 阻止通过运行不受支持的操作系统版本（如 Windows 7）的设备访问组织资源。 对于此方案，组织需创建以下两个条件访问策略：
   - 策略 1：所有用户可以访问所有云应用，并可为“访问控制”授予访问权限，但要求设备标记为合规，或要求设备加入混合 Azure AD。
   - 策略 2：所有用户可以访问所有云应用（包括使用规则表达式“device.operatingSystem equals Windows and device.operatingSystemVersion startsWith "6.1"”的设备筛选器），并可以阻止“访问控制”。
- 在 Teams 电话或 Surface Hub 设备等特定设备上使用时，不需要对特定帐户（如服务帐户）执行多重身份验证。 对于此方案，组织需创建以下两个条件访问策略：
   - 策略 1：除服务帐户以外的所有用户可以访问所有云应用，并可为“访问控制”授予访问权限，但需要执行多重身份验证。
   - 策略 2：选择用户和组并包括仅包含服务帐户的组，他们可以访问所有云应用（但不包括使用规则表达式“device.extensionAttribute2 not equals TeamsPhoneDevice”的设备筛选器）并可以阻止“访问控制”。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

在 Azure 门户中或使用 Microsoft Graph API 创建条件访问策略时，可以选择使用设备筛选器。

> [!IMPORTANT]
> 设备状态和设备筛选器不能在条件访问策略中一起使用。

以下步骤可帮助你创建两个条件访问策略，以支持[常见方案](#common-scenarios)中所述的第一种方案。 

策略 1：拥有“全局管理员”目录角色的所有用户可以访问 Microsoft Azure 管理云应用，并可为“访问控制”授予访问权限，但需要执行多重身份验证，并要求设备标记为合规。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，依次选择“目录角色”、“全局管理员”  。
   
      > [!WARNING]
      > 条件访问策略支持内置角色。 不会为其他角色类型（包括[管理单元范围](../roles/admin-units-assign-roles.md)或[自定义角色](../roles/custom-create.md)）强制实施条件访问策略。

   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，依次选择“选择应用”、“Microsoft Azure 管理”   。
1. 在“访问控制” > “授予”下，依次选择“授予访问权限”、“需要多重身份验证”、“要求设备标记为合规”、“选择”     。
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

策略 2：拥有“全局管理员”目录角色的所有用户可以访问 Microsoft Azure 管理云应用（但不包括使用规则表达式“device.extensionAttribute1 equals SAW”的设备筛选器），并可以阻止“访问控制”。

1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，依次选择“目录角色”、“全局管理员”  。
   
      > [!WARNING]
      > 条件访问策略支持内置角色。 不会为其他角色类型（包括[管理单元范围](../roles/admin-units-assign-roles.md)或[自定义角色](../roles/custom-create.md)）强制实施条件访问策略。

   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，依次选择“选择应用”、“Microsoft Azure 管理”   。
1. 在“条件”下，选择“设备筛选器(预览版)” 。
   1. 将“配置”切换为“是” 。
   1. 将“与规则匹配的设备”设置为“从策略中排除筛选的设备” 。
   1. 将属性设置为 `ExtensionAttribute1`，将运算符设置为 `Equals`，将值设置为 `SAW`。
   1. 选择“完成”。
1. 在“访问控制” > “授权”下，选择“阻止访问”，然后选择“选择”。    
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

### <a name="filters-for-devices-graph-api"></a>设备筛选器 Graph API

设备筛选器 API 目前在 Microsoft Graph beta 终结点中提供，可以使用 https://graph.microsoft.com/beta/identity/conditionalaccess/policies/ 访问它。 可以在创建新的条件访问策略时配置设备筛选器，也可以更新现有策略以配置设备筛选器条件。 若要更新现有策略，可以通过追加现有策略的策略 ID 并执行以下请求正文，针对上述 Microsoft Graph beta 终结点执行 patch 调用。 此处的示例演示如何配置设备筛选器条件，其中排除了未标记为 SAW 设备的设备。 规则语法可以包含多个单一表达式。 若要详细了解语法，请参阅包含多个表达式的规则。 

```json
{
    "conditions": {
        "devices": {
            "deviceFilter": {
                "mode": "exclude",
                "rule": "device.extensionAttribute1 -ne \"SAW\""
            }
        }
    }
}
```

## <a name="supported-operators-and-device-properties-for-filters"></a>筛选器支持的运算符和设备属性

以下设备特性可用于条件访问中的设备筛选器条件。

| 支持的设备特性 | 支持的运算符 | 支持的值 | 示例 |
| --- | --- | --- | --- |
| deviceId | Equals、NotEquals、In、NotIn | GUID 形式的有效 deviceId | (device.deviceid -eq “498c4de7-1aee-4ded-8d5d-000000000000”) |
| displayName | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意字符串 | (device.displayName -contains “ABC”) |
| 制造商 | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意字符串 | (device.manufacturer -startsWith “Microsoft”) |
| mdmAppId | Equals、NotEquals、In、NotIn | 有效的 MDM 应用程序 ID | (device.mdmAppId -in [“0000000a-0000-0000-c000-000000000000”] |
| 模型 | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意字符串 | (device.model -notContains “Surface”) |
| operatingSystem | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 有效的操作系统（如 Windows、iOS 或 Android） | (device.operatingSystem -eq “Windows”) |
| operatingSystemVersion | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 有效的操作系统版本（如 6.1 表示 Windows 7、6.2 表示 Windows 8，或 10.0 表示 Windows 10） | (device.operatingSystemVersion -in [“10.0.18363”, “10.0.19041”, “10.0.19042”]) |
| physicalIds | Contains、NotContains | 例如，所有 Windows Autopilot 设备都在设备 physicalIds 属性中存储 ZTDId（分配给所有导入的 Windows Autopilot 设备的唯一值）。 | (device.devicePhysicalIDs -contains "[ZTDId]") |
| profileType | Equals、NotEquals | 为设备设置的有效配置文件类型。 支持的值为：RegisteredDevice（默认值）、SecureVM（用于 Azure 中已启用 Azure AD 登录的 Windows VM）、Printer（用于打印机）、Shared（用于共享设备）、IoT（用于 IoT 设备） | (device.profileType -notIn [“Printer”, “Shared”, “IoT”] |
| systemLabels | Contains、NotContains | 由系统应用于设备的标签的列表。 一些受支持的值为：AzureResource（用于 Azure 中已启用 Azure AD 登录的 Windows VM）、M365Managed（用于通过 Microsoft 托管桌面管理的设备）、MultiUser（用于共享设备） | （device.systemLabels - 包含“M365Managed”） |
| trustType | Equals、NotEquals | 设备的有效注册状态。 支持的值为：AzureAD（用于已加入 Azure AD 的设备）、ServerAD（用于已加入混合 Azure AD 的设备）、Workplace（用于已注册到 Azure AD 的设备） | (device.trustType -notIn ‘ServerAD, Workplace’) |
| extensionAttribute1-15 | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | extensionAttribute1-15 是可供客户用于设备对象的特性。 客户可以使用自定义值更新 extensionAttribute1 到 extensionAttribute15 中的任何一个特性，并在条件访问中的设备筛选器条件内使用这些特性。 可以使用任何字符串值。 | (device.extensionAttribute1 -eq ‘SAW’) |

## <a name="policy-behavior-with-filters-for-devices"></a>设备筛选器的策略行为

条件访问中的设备筛选器（预览版）条件根据 Azure AD 中已注册设备的设备特性来评估策略，因此，知道在哪些场合下应用或不应用策略非常重要。 下表说明了配置设备筛选器条件时的行为。 

| 设备筛选器条件 | 设备注册状态 | 已应用设备筛选器 
| --- | --- | --- |
| 使用正运算符（Equals、StartsWith、EndsWith、Contains、In）和任何特性的包含/排除模式 | 已取消注册设备 | 否 |
| 使用正运算符（Equals、StartsWith、EndsWith、Contains、In）和除 extensionAttribute1-15 以外的特性的包含/排除模式 | 已注册设备 | 是，如果满足条件 |
| 使用正运算符（Equals、StartsWith、EndsWith、Contains、In）和包括 extensionAttribute1-15 在内的特性的包含/排除模式 | 由 Intune 管理的已注册设备 | 是，如果满足条件 |
| 使用正运算符（Equals、StartsWith、EndsWith、Contains、In）和包括 extensionAttribute1-15 在内的特性的包含/排除模式 | 不由 Intune 管理的已注册设备 | 是，如果满足条件并且设备合规或已加入混合 Azure AD |
| 使用负运算符（NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn）和任何特性的包含/排除模式 | 已取消注册设备 | 是 |
| 使用负运算符（NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn）和除 extensionAttribute1-15 以外的任何特性的包含/排除模式 | 已注册设备 | 是，如果满足条件 |
| 使用负运算符（NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn）和包括 extensionAttribute1-15 在内的任何特性的包含/排除模式 | 由 Intune 管理的已注册设备 | 是，如果满足条件 |
| 使用负运算符（NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn）和包括 extensionAttribute1-15 在内的任何特性的包含/排除模式 | 不由 Intune 管理的已注册设备 | 是，如果满足条件并且设备合规或已加入混合 Azure AD |

> [!IMPORTANT]
> 对于未注册的设备，所传递的唯一设备信息为操作系统、操作系统版本和浏览器。  这意味着，对于未注册的设备以及对设备的筛选器使用负运算符的条件访问策略，这些值之外的任何值都会被计算为空白值。  例如，这样计算未注册的设备：device.displayName -notContains Example。****** 由于未注册的设备会传递空白显示名称，该名称不是 Example 值，因此生成的条件将为 true。

## <a name="next-steps"></a>后续步骤

- [条件访问：条件](concept-conditional-access-conditions.md)
- [常用条件访问策略](concept-conditional-access-policy-common.md)
- [在特权访问故事过程中保护设备](/security/compass/privileged-access-devices)
