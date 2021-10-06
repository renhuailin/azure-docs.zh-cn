---
title: OATH 令牌身份验证方法 - Azure Active Directory
description: 了解如何在 Azure Active Directory 中使用 OATH 令牌来帮助改进和保护登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 449a0ecd02e12816a9a9952fad0446f392ff4af7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352880"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Azure Active Directory 中的身份验证方法 - OATH 令牌 

OATH TOTP（基于时间的一次性密码）是一个开放标准，用于指定如何生成一次性密码 (OTP) 代码。 可以通过使用软件或硬件生成代码来实现 OATH TOTP。 Azure AD 不支持 OATH HOTP，这是另一种代码生成标准。

## <a name="oath-software-tokens"></a>OATH 软件令牌

软件 OATH 令牌通常是应用程序，如 Microsoft Authenticator 应用和其他验证器应用。 Azure AD 生成密钥或种子，密钥或种子会被输入应用中，并用于生成每个 OTP。

Authenticator 应用在设置为发送推送通知后会自动生成代码，以便用户即使在设备没有连接时，也可以有一个备份。 还可以使用通过 OATH TOTP 生成代码的第三方应用程序。

某些 OATH TOTP 硬件令牌是可编程的，这意味着它们不附带密钥或预编程的种子。 这些可编程硬件令牌可以使用从软件令牌设置流获取的密钥或种子进行设置。 客户可以从自己选择的供应商处购买这些令牌，并在供应商的设置过程中使用密钥或种子。

## <a name="oath-hardware-tokens-preview"></a>OATH 硬件令牌（预览版）

Azure AD 支持使用每 30 或 60 秒刷新一次代码的 OATH-TOTP SHA-1 令牌。 客户可以从所选的供应商处购买这些令牌。 

OATH TOTP 硬件令牌通常附带一个在令牌中经过预编程的密钥或种子。 必须将这些密钥输入 Azure AD 中，如以下步骤所述。 密钥限制为 128 个字符，可能不会与所有令牌兼容。 密钥只能包含字符 a-z 或 A-Z，以及数字 2-7，并且必须采用 Base32 编码   。

也可以在软件令牌设置流中使用 Azure AD 设置可重新设定种子的可编程的 OATH TOTP 硬件令牌。

OATH 硬件令牌目前作为公共预览版的一部分受支持。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![将 OATH 令牌上传到 MFA OATH 令牌边栏选项卡](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

获取令牌后，必须以逗号分隔值 (CSV) 文件格式将其上传，内容包括 UPN、序列号、密钥、时间间隔、制造商以及型号，如以下示例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> 请确保 CSV 文件中包含标题行。 如果 UPN 有单引号，请使用另一个引号将其转义。 例如，如果 UPN 是 my’user@domain.com，则在上传文件时将其更改为 my’’user@domain.com。

正确格式化为 CSV 文件后，全局管理员便可以登录 Azure 门户并导航到“Azure Active Directory”>“MFA”>“OATH 令牌”，然后上传生成的 CSV 文件。

根据 CSV 文件的大小，这可能需要花费几分钟来处理。 选择“刷新”按钮可获取当前状态。 如果文件中有任何错误，则可以下载 CSV 文件，其中列出了需要解决的所有错误。 下载的 CSV 文件中的字段名称与上传的版本不同。  

解决所有错误后，管理员可以对令牌选择“激活”，然后输入令牌上显示的 OTP，以此来激活每个密钥。 每 5 分钟最多可以激活 200 个 OATH 令牌。 

用户可以具有最多 5 个 OATH 硬件令牌或验证器应用程序（如配置为可随时使用的 Microsoft Authenticator 应用）的组合。 无法将硬件 OATH 令牌分配给资源租户中的来宾用户。

>[!IMPORTANT]
>此预览在 Azure 政府版或主权云中不受支持。

## <a name="next-steps"></a>后续步骤

详细了解如何使用 [Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview) 配置身份验证方法。
了解与无密码身份验证兼容的 [FIDO2 安全密钥提供程序](concept-authentication-passwordless.md#fido2-security-key-providers)。
