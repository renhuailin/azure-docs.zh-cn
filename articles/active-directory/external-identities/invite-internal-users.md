---
title: 邀请内部用户进行 B2B 协作-Azure AD
description: 如果有适用于合作伙伴、分销商、供应商、供应商和其他来宾的内部用户帐户，则可以通过邀请他们使用自己的外部凭据或登录名进行登录，来 Azure AD B2B 协作。 使用 PowerShell 或 Microsoft Graph 邀请 API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576424"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>邀请内部用户进行 B2B 协作

在 Azure AD B2B 协作的可用性之前，组织可以通过设置内部凭据与分销商、供应商、供应商和其他来宾用户合作。 如果有类似于内部来宾用户，可以邀请他们使用 B2B 协作。 这些 B2B 来宾用户将能够使用自己的标识和凭据登录，无需维护密码或管理帐户生命周期。

通过向现有内部帐户发送邀请，你可以保留该用户的对象 ID、UPN、组成员身份和应用分配。 无需手动删除并重新邀请用户或重新分配资源。 若要邀请用户，可以使用邀请 API 同时传递内部用户对象和来宾用户的电子邮件地址以及邀请。 用户接受邀请后，B2B 服务会将现有的内部用户对象更改为 B2B 用户。 今后，用户必须使用其 B2B 凭据登录到云资源服务。

## <a name="things-to-consider"></a>注意事项

- **访问本地资源**：在将用户邀请到 B2B 协作后，他们仍可使用其内部凭据访问本地资源。 您可以通过重置或更改内部帐户的密码来防止这种情况。 例外情况是 [电子邮件一次性密码身份验证](one-time-passcode.md);如果用户的身份验证方法更改为一次性密码，则他们将无法再使用其内部凭据。

- **计费**：此功能不会更改用户的 UserType，因此它不会将用户的计费模式自动切换为 [每月活动用户 (MAU) 定价](external-identities-pricing.md)。 若要激活用户的 MAU 定价，请将用户的 UserType 更改为 `guest` 。 另请注意，必须将 Azure AD 租户 [链接到 Azure 订阅](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) 才能激活 MAU 计费。

- **邀请是单向** 的：可以邀请内部用户使用 B2B 协作，但在添加 b2b 凭据后，不能将其删除。 若要将用户更改回仅限内部的用户，则需要删除用户对象并创建一个新的用户对象。

- **团队**：当用户使用其外部凭据访问团队时，其租户最初在团队租户选取器中不可用。 用户可以使用包含租户上下文的 URL 访问团队，例如： `https://team.microsoft.com/?tenantId=<TenantId>` 。 然后，租户将在团队租户选取器中变为可用。

- **本地同步的用户**：对于在本地与云之间同步的用户帐户，本地目录在被邀请使用 B2B 协作后仍会保留授权源。 对本地帐户所做的任何更改都将同步到云帐户，包括禁用或删除帐户。 因此，只需删除本地帐户，就不能阻止用户登录到本地帐户，同时保留其云帐户。 相反，你可以将本地帐户密码设置为随机 GUID 或其他未知值。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>如何将内部用户邀请到 B2B 协作

可以使用 PowerShell 或邀请 API 将 B2B 邀请发送给内部用户。 请确保要用于邀请的电子邮件地址设置为内部用户对象上的外部电子邮件地址。

- 您必须使用该邀请的 User. Mail 属性中的电子邮件地址。
- 用户的 Mail 属性中的域必须与他们用来登录的帐户相匹配。 否则，某些服务（例如团队）将无法对用户进行身份验证。

默认情况下，邀请会向用户发送一封电子邮件，告知他们已被邀请，但你可以禁止发送此电子邮件并改为发送你自己的电子邮件。

> [!NOTE]
> 若要发送你自己的电子邮件或其他通信，你可以将 `New-AzureADMSInvitation` 与一起使用以 `-SendInvitationMessage:$false` 以无提示方式邀请用户，然后将你自己的电子邮件发送给已转换的用户。 请参阅 [AZURE AD B2B 协作 API 和自定义](customize-invitation-api.md)。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>使用 PowerShell 发送 B2B 邀请

需要 Azure AD PowerShell 模块版本2.0.2.130 或更高版本。 使用以下命令更新到最新的 AzureAD PowerShell 模块，并邀请内部用户使用 B2B 协作：

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>使用邀请 API 发送 B2B 邀请

下面的示例演示了如何调用邀请 API 来邀请内部用户作为 B2B 用户。

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

对 API 的响应与你邀请新的来宾用户访问目录时获得的响应相同。
## <a name="next-steps"></a>后续步骤

- [B2B 协作邀请兑换](redemption-experience.md)
