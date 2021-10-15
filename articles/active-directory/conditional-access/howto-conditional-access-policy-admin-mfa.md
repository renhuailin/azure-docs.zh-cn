---
title: 条件访问 - 需要对管理员进行 MFA - Azure Active Directory
description: 创建要求管理员进行多重身份验证的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7abf8738fd93cc84923abb3d9e5308599c4220da
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272484"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>条件访问：要求对管理员执行 MFA

分配了管理权限的帐户是攻击者的目标。 要求在这些帐户上进行多重身份验证 (MFA) 后，就可以轻松降低这些帐户受攻击的风险。

Microsoft 建议你至少要求对以下角色进行 MFA：

- 全局管理员
- 应用程序管理员
- 身份验证管理员
- 计费管理员
- 云应用程序管理员
- 条件访问管理员
- Exchange 管理员
- 支持管理员
- 密码管理员
- 特权身份验证管理员
- 特权角色管理员
- 安全管理员
- SharePoint 管理员
- 用户管理员

组织可以根据情况选择包括或排除角色。

## <a name="user-exclusions"></a>排除用户

条件访问策略是强大的工具，建议从策略中排除以下帐户：

- 紧急访问帐户或不受限帐户，用于防止租户范围的帐户锁定   。 在极少数情况下，所有管理员都被锁定在租户外，紧急访问管理帐户可用于登录租户，以采取措施来恢复访问。
   - 有关详细信息，可参阅[管理 Azure AD 中的紧急访问帐户](../roles/security-emergency-access.md)一文。
- **服务帐户** 和 **服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是非交互性帐户，不绑定到任何特定用户。 它们通常由允许对应用程序进行编程访问的后端服务使用，但也用于出于管理目的登录到系统。 应该排除这样的服务帐户，因为无法以编程方式完成 MFA。 服务主体进行的调用不被条件访问阻止。
   - 如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将有助于创建条件访问策略，该策略要求那些分配的管理角色执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下选择“目录角色”，然后选择内置角色，例如： 
      - 全局管理员
      - 应用程序管理员
      - 身份验证管理员
      - 计费管理员
      - 云应用程序管理员
      - 条件访问管理员
      - Exchange 管理员
      - 支持管理员
      - 密码管理员
      - 特权身份验证管理员
      - 特权角色管理员
      - 安全管理员
      - SharePoint 管理员
      - 用户管理员
   
      > [!WARNING]
      > 条件访问策略支持内置角色。 不会为其他角色类型（包括[管理单元范围](../roles/admin-units-assign-roles.md)或[自定义角色](../roles/custom-create.md)）强制实施条件访问策略。

   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。
   1. 选择“完成”。
1. 在“云应用或操作” > “包含”下方，选择“所有云应用” ，然后选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“授予访问权限”、“需要多重身份验证”、“选择”。  
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
