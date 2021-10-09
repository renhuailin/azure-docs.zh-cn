---
title: 条件访问需要受管理设备 - Azure Active Directory
description: 了解如何配置 Azure Active Directory (Azure AD) 基于设备的条件访问策略来要求使用受管理设备进行云应用访问。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61b5bb4a8cdfa8dddcbdb08283245a6e8d7c93cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601190"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>如何：通过条件访问要求使用受管理设备进行云应用访问

在移动优先、云优先的世界中，使用 Azure Active Directory (Azure AD) 可以实现从任意位置以单一登录方式登录到应用和服务。 经授权的用户可以从一系列设备（包括移动设备和个人设备）访问云应用。 但是，许多环境中至少有几个应用会要求仅通过满足你的安全性和符合性标准的设备进行访问。 这些设备也称为受管理设备。 

本文介绍了如何配置条件访问策略来要求使用受管理设备访问环境中的特定云应用。 

## <a name="prerequisites"></a>先决条件

要求使用受管理设备进行云应用访问就必须将 **Azure AD 条件访问** 与 **Azure AD 设备管理** 结合在一起。 如果尚不熟悉其中的某项功能，应该先阅读以下主题:

- **[Azure Active Directory 中的条件访问](./overview.md)** - 此文提供了条件访问的概念性概述和相关术语。
- **[Azure Active Directory 中的设备管理简介](../devices/overview.md)** - 此文章概述了可以用来将设备置于组织控制下的各种选项。 
- 如需在 Windows 10 创意者更新（版本 1703）或更高版本中获得 Chrome 支持，请安装 [Windows 10 Accounts 扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 当条件访问策略需要特定于设备的详细信息时，此扩展是必需的。
- 对于 Firefox 支持，请在 Windows 10 版本 1809 或更高版本中启动 Firefox 91，配置 [Windows SSO](https://support.mozilla.org/kb/windows-sso) 。

>[!NOTE] 
> 我们建议使用基于 Azure AD 设备的条件访问策略，在初始设备身份验证后获得最佳实施。 这包括在设备不符合合规性和设备代码流时关闭会话。


## <a name="scenario-description"></a>方案描述

掌控安全性与工作效率之间的平衡是一个难题。 扩大用来访问云资源的受支持设备的范围有助于提高用户的工作效率。 另一方面，你可能不希望具有未知保护级别的设备访问你的环境中的某些资源。 对于受影响的资源，你应当要求用户只能使用受管理设备访问它们。 

使用 Azure AD 条件访问，可以通过进行授权的以下单个策略来解决此要求：

- 授予对所选云应用的访问权限
- 为所选用户和组授予权限
- 要求使用受管理设备

## <a name="managed-devices"></a>托管设备  

简而言之，受管理设备是指处于 *某种* 组织控制之下的设备。 在 Azure AD 中，受管理设备的先决条件是它已向 Azure AD 注册。 注册设备时会以设备对象的形式为设备创建标识。 Azure 使用此对象来跟踪设备的状态信息。 作为 Azure AD 管理员，你可以使用此对象切换（启用/禁用）设备状态。
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Azure A D 中“设备”窗格的屏幕截图。突出显示了“启用”和“禁用”项。" border="false":::

若要向 Azure AD 注册设备，你有三种选择： 

- **Azure AD 注册设备**：向 Azure AD 注册个人设备
- **加入 Azure AD 的设备** - 向 Azure AD 注册未加入本地 AD 的组织 Windows 10 设备。 
- **加入混合 Azure AD 的设备** - 向 Azure AD 注册已加入本地 AD 的 Windows 10 或受支持的低级别设备。

[什么是设备标识？](../devices/overview.md)一文中讨论了这三个选项

若要成为受管理设备，注册设备必须是 **加入混合 Azure AD 的设备** 或者是 **已标记为合规的设备**。  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="“Azure A D 授权”窗格的屏幕截图。已选择“授予访问权限”，已选中设备需兼容且已加入混合 Azure A D 的复选框。" border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>要求使用加入混合 Azure AD 的设备

在条件访问策略中，可以选择“要求使用加入混合 Azure AD 的设备”来声明只能使用受管理设备访问所选云应用。 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="“Azure A D 授权”窗格的屏幕截图。已选择“授予访问权限”。也已选中要求设备加入混合 Azure A D 的复选框。" border="false":::

此设置仅适用于已加入本地 AD 的 Windows 10 或低级别设备（例如 Windows 7 或 Windows 8）。 你只能使用混合 Azure AD 加入功能向 Azure AD 注册这些设备，这是一种注册 Windows 10 设备的[自动化过程](../devices/hybrid-azuread-join-plan.md)。 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="表列出设备的名称、启用状态、O S、版本、联接类型、所有者、M D M 和符合状态。符合状态为“否”。" border="false":::

怎样使加入混合 Azure AD 的设备成为受管理设备？  对于加入本地 AD 的设备，假定使用管理解决方案（如 Configuration Manager）或组策略 (GP) 对这些设备进行控制来管理它们 。 由于 Azure AD 无法确定是否已向设备应用这些方法中的任何一种，因此，在要求使用受管理设备的情况下，要求使用加入混合 Azure AD 的设备是一种相对较弱的机制。 如果加入本地域的设备同时也是加入混合 Azure AD 的设备，则由管理员判断应用于此类设备的方法是否强大到足以使其成为受管理设备。

## <a name="require-device-to-be-marked-as-compliant"></a>要求将设备标记为合规

“要求将设备标记为合规”选项是一种用于请求受管理设备的最强大的形式。

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="“Azure A D 授权”窗格的屏幕截图。已选择“授予访问权限”。还选中了要求将设备标记为合规的复选框。" border="false":::

此选项要求向 Azure AD 注册设备，此外还要求通过以下方式将该设备标记为合规：
         
- Intune
- 第三方移动设备管理 (MDM) 系统，该系统通过 Azure AD 集成管理 Windows 10 设备。 不支持除 Windows 10 以外的设备 OS 类型的第三方 MDM 系统。
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="表列出设备的名称、启用状态、O S、版本、联接类型、所有者、M D M 和符合状态。突出显示了符合状态。" border="false":::

对于标记为合规的设备，你可以假设： 

- 员工用来访问公司数据的移动设备是受管理设备
- 员工使用的移动应用是受管理应用
- 通过帮助控制员工访问和共享公司信息的方式，为公司信息提供保护
- 该设备及其应用符合公司安全要求

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>方案：需要 iOS 和 Android 设备的设备注册

在这种情况下，Contoso 已决定对 Microsoft 365 资源的所有移动访问都必须使用已注册的设备。 其所有用户已使用 Azure AD 凭据登录，并获得了分配的许可证，其中包括 Azure AD Premium P1 或 P2 以及 Microsoft Intune。

组织必须完成以下步骤才能要求使用已注册的移动设备。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下选择“所有用户”，或选择你希望对其应用此策略的具体“用户和组”。    
   1. 选择“完成”  。
1. 在“云应用或操作” > “包括”下，选择“Office 365”。
1. 在“条件”下，选择“设备平台”。  
   1. 将“配置”设置为“是”。  
   1. 包括 Android 和 iOS。  
1. 在“访问控制” > “授予”下，选择以下选项：
   - **要求将设备标记为合规**
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建并启用策略。

### <a name="known-behavior"></a>已知行为

使用[设备代码 OAuth 流时](../develop/v2-oauth2-device-code.md)，不支持要求受管理设备授予控制或设备状态条件。 这是因为执行身份验证的设备无法向提供代码的设备提供其设备状态，并且令牌中的设备状态会锁定到执行身份验证的设备。 改为使用“需要多重身份验证授权控制”。

在 Windows 7、iOS、Android、macOS 和某些第三方 Web 浏览器上，Azure AD 使用客户端证书来标识设备，该证书是在向 Azure AD 注册设备时预配的。 用户首次通过浏览器登录时，系统会提示用户选择此证书。 最终用户必须选择此证书才能继续使用浏览器。

## <a name="next-steps"></a>后续步骤

[先评估条件访问策略的影响，再使用仅限报告模式广泛启用此策略](concept-conditional-access-report-only.md)。
