---
title: 微移用户以设置 Microsoft Authenticator 应用（预览版）- Azure Active Directory
description: 了解如何将组织从不太安全的身份验证方法迁移到 Microsoft Authenticator 应用
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c83c4c60028c77033227411d68aeb576a9b43a46
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707867"
---
# <a name="how-to-nudge-users-to-set-up-microsoft-authenticator-preview---microsoft-authenticator-app"></a>如何微移用户以设置 Microsoft Authenticator（预览版）- Microsoft Authenticator 应用

你可以在登录过程中微移用户以设置 Microsoft Authenticator。 用户将完成其常规登录，照常执行多重身份验证，然后系统会提示设置 Microsoft Authenticator。 可以包括或排除用户或组，以控制微移谁来设置应用。 这允许目标市场活动将用户从不太安全的身份验证方法迁移到 Microsoft Authenticator。  

除了选择可以微移的用户外，还可以定义用户可以延迟或“推迟”微移的天数。 如果用户点击“以后再说”推迟应用安装，则他们将在推迟持续时间过后的下一次 MFA 尝试中再次被微移。 

## <a name="prerequisites"></a>先决条件 

- 你的组织必须已启用 Azure MFA。 
- 用户不能在其帐户上为推送通知设置 Microsoft Authenticator。 
- 管理员需要使用以下策略之一为用户启用 Microsoft Authenticator：  
  - MFA 注册策略：需要为用户启用“通过移动应用发送通知”。  
  - 身份验证方法策略：需要为用户启用 Microsoft Authenticator，并将身份验证模式设置为“任何”或“推送” 。 如果策略设置为“无密码”，则用户不符合微移的条件。 

## <a name="user-experience"></a>用户体验

1. 用户使用 Azure MFA 成功执行 MFA。 

1. 用户看到设置 Microsoft Authenticator 应用以改善其登录体验的提示。 注意：只有允许 Microsoft Authenticator 推送通知的用户和当前未设置的用户才会看到提示。 

   ![用户执行多重身份验证](./media/how-to-nudge-authenticator-app/user-mfa.png)

1. 用户点击“下一步”并逐步完成 Microsoft Authenticator 安装。 
   1. 首先，下载此应用。  

      ![用户下载 Microsoft Authenticator](./media/how-to-nudge-authenticator-app/download.png)

   1. 了解如何设置 Microsoft Authenticator。 
   
      ![用户设置 Microsoft Authenticator](./media/how-to-nudge-authenticator-app/setup.png)

   1. 扫描 QR 码。 

      ![用户扫描 QR 码](./media/how-to-nudge-authenticator-app/scan.png)

   1. 批准测试通知。

      ![用户批准测试通知](./media/how-to-nudge-authenticator-app/test.png)

   1. 通知已批准。

      ![确认批准](./media/how-to-nudge-authenticator-app/approved.png)

   1. Authenticator 应用现已成功设置为用户的默认登录方法。

      ![安装完成](./media/how-to-nudge-authenticator-app/finish.png)

1. 如果用户不想安装 Authenticator 应用，则他们可以点击“以后再说”以将提示推迟几天，这可由管理员定义。 
 
   ![推迟安装](./media/how-to-nudge-authenticator-app/snooze.png)

## <a name="enable-the-nudge-policy"></a>启用微移策略

若要启用微移，必须通过图形 API 使用身份验证方法策略。 全局管理员和身份验证方法策略管理员可以更新策略 。 

使用 Graph 浏览器配置策略：

1. 登录到 Graph 浏览器并确保你已同意 Policy.Read.All 和 Policy.ReadWrite.AuthenticationMethod 权限 。

   打开“权限”面板：

   ![Graph 浏览器的屏幕截图](./media/how-to-nudge-authenticator-app/permissions.png)

1. 检索身份验证方法策略：`GET https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

1. 更新策略的 registrationEnforcement 和 authenticationMethodsRegistrationCampaign 部分以启用对用户或组的微移。

   ![市场活动部分](./media/how-to-nudge-authenticator-app/campaign.png)

若要更新策略，请仅使用更新的 registrationEnforcement 部分对身份验证方法策略执行 PATCH：`PATCH https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

下表列出了 authenticationMethodsRegistrationCampaign 属性。

| 名称 | 可能值 | 说明 |
|------|-----------------|-------------|
| state |   "enabled"<br>"disabled"<br>"default" | 使你可以启用或禁用功能。<br>如果未显式设置配置，并且将为此设置使用 Azure AD 默认值，则使用默认值。 当前映射到“已禁用”。<br>根据需要将状态更改为“已启用”或“已禁用”。  |
| snoozeDurationInDays | 范围：0 - 14 | 定义用户在多少天后会再次看到微移。<br>如果该值为 0，则将在每次 MFA 尝试期间微移用户。<br>默认值：1 天 |
| includeTargets | 空值 | 允许包含要使功能面向的不同用户和组。 |
| excludeTargets | 空值 | 允许排除要从功能中省略的不同用户和组。 如果用户同时位于排除的组和包含的组中，则将从该功能中排除该用户。|

下表列出了 includeTargets 属性。

| 名称 | 可能值 | 说明 |
|------|-----------------|-------------|
| targetType| "user"<br>“Group” | 目标实体的类型。 |
| ID | Guid 标识符 | 目标用户或组的 ID。 |
| targetedAuthenticationMethod | "microsoftAuthenticator" | 系统将提示用户注册的身份验证方法。 唯一允许的值为 "microsoftAuthenticator"。 |

下表列出了 excludeTargets 属性。

| 名称       | 可能值   | 说明                           |
|------------|-------------------|---------------------------------------|
| targetType | "user"<br>“Group” | 目标实体的类型。          |
| ID         | 一个字符串          | 目标用户或组的 ID。 |

### <a name="examples"></a>示例

下面是一些可用于入门的示例 JSON！ 

- 包括所有用户 
  
  如果要在租户中包括所有用户，只需[下载此 JSON](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/All%20Users%20Enabled.json)，并将其粘贴到 Graph 浏览器中，然后在终结点上运行 `PATCH`。 

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [],
              "includeTargets": [
                  {
                      "id": "all_users",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

- 包括特定用户或用户组

  如果要在租户中包括某些用户或组，请[下载此 JSON](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes.json)，并使用用户和组的相关 GUID 对其进行更新。 然后，在 Graph 浏览器中粘贴 JSON 并在终结点上运行 `PATCH`。 

  ```json
  {
  "registrationEnforcement": {
        "authenticationMethodsRegistrationCampaign": {
            "snoozeDurationInDays": 0,
            "state": "enabled",
            "excludeTargets": [],
            "includeTargets": [
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "group",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                },
        {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "user",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                }
            ]
        }
    }
  ```

- 包括和排除特定用户/用户组

  如果要在租户中包括和排除某些用户/用户组，请[下载此 JSON](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes%20and%20Excludes.json) 并将其粘贴到 Graph 浏览器中，然后在终结点上运行 `PATCH`。 为用户和组输入正确的 GUID。

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group"
                  },
        {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user"
                  }
              ],
              "includeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  },
        {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

### <a name="identify-the-guids-of-users-to-insert-in-the-jsons"></a>确定要插入到 JSON 中的用户的 GUID

1. 导航到 Azure 门户。
1. 点击“Azure Active Directory”。
1. 在“管理”边栏选项卡中，点击“用户” 。
1. 在“用户”页中，确定要作为目标的特定用户。
1. 点击特定用户时，将显示其“对象 ID”，即用户的 GUID。

   ![用户对象 ID](./media/how-to-nudge-authenticator-app/object-id.png)

### <a name="identify-the-guids-of-groups-to-insert-in-the-jsons"></a>确定要插入到 JSON 中的组的 GUID

1. 导航到 Azure 门户。
1. 点击“Azure Active Directory”。
1. 在“管理”边栏选项卡中，点击“组” 。
1. 在“组”页中，确定要作为目标的特定组。
1. 点击组并获取“对象 ID”。

   ![微移组](./media/how-to-nudge-authenticator-app/group.png)

<!---comment out PS until ready>

### PowerShell

1. Install the module.
1. Ensure you pass the right roles:
   
   ```powershell
   Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"
   ```

1. Select the beta profile.
1. Call `Update-MgPolicyAuthenticationMethod`.

<---->

## <a name="limitations"></a>限制

微移不会出现在运行 Android 或 iOS 的移动设备上。

## <a name="frequently-asked-questions"></a>常见问题

**此功能是否可用于 MFA 服务器？** 否。 此功能仅适用于使用 Azure MFA 的用户。 

**市场活动会运行多长时间？** 可以根据需要使用 API 来启用市场活动。 每当你想要完成运行市场活动时，只需使用 API 来禁用该市场活动。  
 
**每组用户是否可以具有不同的推迟持续时间？** 否。 提示的推迟持续时间是租户范围的设置，适用于范围内的所有组。 

**是否可以微移用户以设置无密码手机登录？** 此功能旨在使管理员能够使用 Authenticator 应用为用户设置 MFA，而不是无密码手机登录。  

**安装了第三方验证器应用的用户是否能看到微移？** 如果此用户没有为推送通知设置 Microsoft Authenticator 应用，并按策略将其启用，用户就可以看到微移。 

**仅为 TOTP 代码安装了 Microsoft Authenticator 应用的用户会看到微移吗？** 是的。 如果没有为推送通知设置 Microsoft Authenticator 应用，并且用户按策略将其启用，用户就会看到微移。

**如果用户刚刚完成了 MFA 注册，他们是否会在同一登录会话中被微移？** 否。 为提供良好的用户体验，将不会在其注册了其他身份验证方法的同一会话中微移用户以设置 Authenticator。  

**我是否可以微移用户以注册另一种身份验证方法？** 否。 目前，该功能仅旨在微移用户以设置 Microsoft Authenticator 应用。 

**是否有办法隐藏推迟选项并强制用户安装 Authenticator 应用？**  
没有办法隐藏微移上的推迟选项。 可以将 snoozeDuration 设置为 0，这将确保用户在每次 MFA 尝试期间都能看到微移。  

**如果我不使用 Azure MFA，是否可以微移我的用户？** 否。 微移仅适用于使用 Azure MFA 服务执行 MFA 的用户。 

**我的租户中的来宾/B2B 用户是否会被微移？** 是的。 如果他们已被限定为使用策略进行微移，就会被微移。 

**如果用户关闭浏览器怎么办？** 与推迟相同。


## <a name="next-steps"></a>后续步骤

[启用 Microsoft Authenticator 应用的无密码登录](howto-authentication-passwordless-phone.md)