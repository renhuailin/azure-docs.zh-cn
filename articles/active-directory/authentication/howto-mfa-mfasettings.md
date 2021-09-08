---
title: 配置 Azure AD 多重身份验证 - Azure Active Directory
description: 了解如何在 Azure 门户中配置 Azure AD 多重身份验证的设置
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/12/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: d5aa2a2eba1cf6123d81813cab00d2ac7c5309b4
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835365"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>配置 Azure AD 多重身份验证设置

若要自定义 Azure AD 多重身份验证的最终用户体验，可以配置帐户锁定阈值或者欺诈警报和通知等设置的选项。 有些设置直接在 Azure Active Directory (Azure AD) 的 Azure 门户中提供，而有些设置则是在单独的 Azure AD 多重身份验证门户中提供。

Azure 门户中提供了以下 Azure AD 多重身份验证设置：

| 功能 | 说明 |
| ------- | ----------- |
| [帐户锁定](#account-lockout) | 如果连续有过多的身份验证尝试遭到拒绝，则会暂时锁定帐户，使其无法使用 Azure AD 多重身份验证。 此功能仅适用于输入 PIN 进行身份验证的用户。 （MFA 服务器） |
| [阻止/解除阻止用户](#block-and-unblock-users) | 阻止特定用户，使其无法接收 Azure AD 多重身份验证请求。 自动拒绝任何针对受阻止用户的身份验证尝试。 用户将被阻止 90 天（从阻止日开始算），或者阻止到手动解除阻止为止。 |
| [欺诈警报](#fraud-alert) | 配置允许用户报告欺诈性验证请求的设置。 |
| [通知](#notifications) | 允许通过 MFA 服务器来通知事件。 |
| [OATH 令牌](concept-authentication-oath-tokens.md) | 用于在基于云的 Azure AD MFA 环境中管理用户的 OATH 令牌。 |
| [电话呼叫设置](#phone-call-settings) | 为云和本地环境配置与电话呼叫和问候语相关的设置。 |
| 提供程序 | 此项会显示任何现有的与帐户相关联的身份验证提供程序。 自 2018 年 9 月 1 日起，可能无法创建新的身份验证提供程序 |

![Azure 门户 - Azure AD 多重身份验证设置](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>帐户锁定

为了防止攻击者在攻击过程中采用反复尝试 MFA 的手段，可以通过帐户锁定设置来指定将帐户锁定一段时间之前允许的失败尝试次数。 仅当根据 MFA 提示输入了 PIN 码时，才应用帐户锁定设置。

提供了下列设置：

* 触发帐户锁定之前所要达到的 MFA 拒绝次数
* 在重置帐户锁定计数器之前所要经过的分钟数
* 在自动解除阻止帐户之前所要经过的分钟数

若要配置帐户锁定设置，请完成以下设置：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “帐户锁定”。   
1. 输入环境所需的值，然后选择“保存”。

    ![Azure 门户中帐户锁定设置的屏幕截图](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>阻止和解除阻止用户

如果用户的设备丢失或被盗，你可以阻止关联的帐户进行 Azure AD 多重身份验证尝试。 系统会自动拒绝受阻止用户进行的任何 Azure AD 多重身份验证尝试。 用户将在阻止日起 90 天内受到阻止。 我们已发布有关[如何在租户中阻止和解除阻止用户](https://www.youtube.com/watch?v=WdeE1On4S1o)的视频，其中演示了如何执行此操作。

### <a name="block-a-user"></a>阻止用户

若要阻止用户，请完成以下步骤，或者观看[此简短视频](https://www.youtube.com/watch?v=WdeE1On4S1o&feature=youtu.be)

1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “阻止/解除阻止用户”   。
1. 选择“添加”以阻止用户。
1. 输入 `username@domain.com` 作为受阻止用户的用户名，然后在“原因”字段中提供注释。
1. 准备就绪后，选择“确定”以阻止该用户。

### <a name="unblock-a-user"></a>解除阻止用户

若要解除阻止用户，请完成以下步骤：

1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “阻止/解除阻止用户”   。
1. 在所需用户旁边的“操作”列中，选择“解除阻止”。
1. 在“解除阻止原因”字段输入注释。
1. 准备就绪后，选择“确定”以解除阻止该用户。

## <a name="fraud-alert"></a>欺诈警报

用户可以通过欺诈警报功能来报告有人以欺诈手段尝试访问其资源。 收到未知且可疑的 MFA 提示后，用户可以使用 Microsoft Authenticator 应用或通过电话来报告欺诈企图。

可使用以下欺诈警报配置选项：

* **自动阻止报告欺诈行为的用户**：如果某个用户报告了欺诈行为，系统将阻止其用户帐户进行 Azure AD MFA 身份验证尝试 90 天，或者直到管理员解除阻止其帐户为止。 管理员可以使用登录报表查看登录并采取相应的操作阻止将来的欺诈。 然后管理员可以[解除阻止](#unblock-a-user)用户帐户。
* **用于在初始问候期间报告欺诈行为的代码** - 当用户接到执行多重身份验证的电话时，他们通常会按 **#** 来确认登录。 如果他们想要报告欺诈，则可在按 **#** 之前输入代码。 此代码默认为 **0**，但可以自定义此代码。

   > [!NOTE]
   > Microsoft 的默认问候语音将指示用户按 0# 以提交欺诈警报。 如果要使用 0 以外的代码，请录制并上传自定义语音问候，为用户提供相应指示。

若要启用和配置欺诈警报，请完成以下步骤：

1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “欺诈警报”。   
1. 将“允许用户提交欺诈警报”设置为“打开”。 
1. 根据需要配置“自动阻止报告欺诈行为的用户”或“用于在初始问候期间报告欺诈行为的代码”设置。 
1. 准备就绪后，选择“保存”。

### <a name="view-fraud-reports"></a>查看欺诈报告

当用户报告欺诈时，该事件会显示在登录报告（作为被用户拒绝的登录）和审核日志中。

- 若要在登录报告中查看欺诈报告，请单击“Azure Active Directory” > “登录” > “身份验证详细信息”。 欺诈报告是标准 Azure AD 登录报告的一部分，在“结果详细信息”中显示为“已拒绝 MFA，输入了欺诈代码”。

- 若要查看审核日志中的欺诈报告，请单击“Azure Active Directory” > “审核日志”。 根据欺诈报告的租户级设置，欺诈报告将显示在“已报告欺诈 - 用户因 MFA 而被阻止”或“已报告欺诈 - 未采取任何措施”活动类型下。
 
## <a name="notifications"></a>通知

可以在用户报告欺诈警报时配置电子邮件通知。 这些通知通常会发送给标识管理员，因为用户的帐户凭据有可能已泄露。 以下示例显示了欺诈警报通知电子邮件的外观：

![欺诈警报通知电子邮件示例](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

若要配置欺诈警报通知，请完成以下设置：

1. 浏览到“Azure Active Directory” > “安全性” > “多重身份验证” > “通知”。   
1. 输入要添加到下一个框中的电子邮件地址。
1. 若要删除现有的电子邮件地址，请选择所需电子邮件地址旁边的“...”选项，然后选择“删除”。 
1. 准备就绪后，选择“保存”。

## <a name="oath-tokens"></a>OATH 令牌

Azure AD 支持使用每 30 或 60 秒刷新一次代码的 OATH-TOTP SHA-1 令牌。 客户可以从所选的供应商处购买这些令牌。

OATH TOTP 硬件令牌通常附带一个在令牌中经过预编程的密钥或种子。 必须将这些密钥输入 Azure AD 中，如以下步骤所述。 密钥限制为 128 个字符，可能不会与所有令牌兼容。 密钥只能包含字符“a”-“z”或“A”-“Z”，以及数字“1”-“7”，并且必须采用“Base32”编码   。

也可以在软件令牌设置流中使用 Azure AD 设置可重新设定种子的可编程的 OATH TOTP 硬件令牌。

OATH 硬件令牌目前作为公共预览版的一部分受支持。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![将 OATH 令牌上传到 MFA OATH 令牌边栏选项卡](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

获取令牌后，必须以逗号分隔值 (CSV) 文件格式将其上传，包括 UPN、序列号、密钥、时间间隔、制造商以及型号，如以下示例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 请确保 CSV 文件中包含标题行。

正确格式化为 CSV 文件后，管理员便可以登录 Azure 门户并导航到“Azure Active Directory”>“MFA”>“OATH 令牌”，然后上传生成的 CSV 文件。

根据 CSV 文件的大小，这可能需要花费几分钟来处理。 选择“刷新”按钮可获取当前状态。 如果文件中有任何错误，则可以下载 CSV 文件，其中列出了需要解决的所有错误。 下载的 CSV 文件中的字段名称与上传的版本不同。

解决所有错误后，管理员可以对令牌选择“激活”，然后输入令牌上显示的 OTP，以此来激活每个密钥。

用户可以具有最多 5 个 OATH 硬件令牌或验证器应用程序（如配置为可随时使用的 Microsoft Authenticator 应用）的组合。

## <a name="phone-call-settings"></a>电话呼叫设置

如果用户接到了 MFA 提示电话，则你可以配置他们的体验，例如来电显示或他们听到的问候语音。

在美国，如果尚未配置 MFA 来电显示，则 Microsoft 语音呼叫的号码如下。 如果正在使用垃圾邮件筛选器，请务必排除该号码：

* *+1 (855) 330 8653*

> [!NOTE]
> 通过公共电话网络拨打 Azure AD 多重身份验证电话时，有时会通过不支持来电显示的运营商路由电话。 因此，尽管 Azure AD 多重身份验证始终会发送呼叫号码，但并不保证会显示该号码。 这一点适用于电话呼叫以及 Azure AD 多重身份验证提供的短信。 如果需要验证短信是否来自 Azure AD 多重身份验证，请参阅[用于发送消息的短信短代码是什么？](multi-factor-authentication-faq.yml#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users-)

若要配置自己的来电显示号码，请完成以下步骤：

1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “电话呼叫设置”。   
1. 将“MFA 来电显示号码”设置为你希望用户在其电话上看到的号码。 仅允许美国的编号。
1. 准备就绪后，选择“保存”。

### <a name="custom-voice-messages"></a>自定义语音消息

可以使用自定义语音消息功能，将自己的录音或问候语用于 Azure AD 多重身份验证。 这些消息可与默认的 Microsoft 录音一起使用，也可以取而代之。

在开始之前，请注意以下限制：

* 支持的文件格式为 *.wav* 和 *.mp3*。
* 文件大小上限是 1 MB。
* 身份验证消息应少于 20 秒。 超过 20 秒的消息可能导致验证失败。 用户可能不会在消息播放结束前作出响应，导致验证超时。

### <a name="custom-message-language-behavior"></a>自定义消息语言行为

向用户播放自定义语音消息时，消息的语言取决于以下因素：

* 当前用户的语言。
  * 用户的浏览器检测到的语言。
  * 其他身份验证方案可能操作方式不同。
* 任何可用的自定义消息的语言。
  * 添加自定义消息时，由管理员选择此语言。

例如，如果只有一条采用德语的自定义消息：

* 以德语进行身份验证的用户将听到自定义德语消息。
* 以英语进行身份验证的用户将听到标准英语消息。

### <a name="custom-voice-message-defaults"></a>自定义语音消息默认值

可使用以下示例脚本创建你自己的自定义消息。 如果你未配置自己的自定义消息，默认将使用这些短语：

| 消息名 | Script |
| --- | --- |
| 身份验证成功 | 你已成功通过登录验证。 再见。 |
| 分机提示 | 感谢你使用 Microsoft 登录验证系统。 请按井号键继续。 |
| 欺诈确认 | 已提交欺诈警报。 若要解除锁定你的帐户，请联系你公司的 IT 支持人员。 |
| 欺诈问候语（标准） | 感谢你使用 Microsoft 登录验证系统。 请按井号键完成验证。 如果此次验证并非由你启动，则表明可能有人试图访问你的帐户。 请按 0 和井号键提交欺诈警报。 这将通知你公司的 IT 团队，并阻止进一步的验证尝试。 |
| 已举报欺诈行为    已提交欺诈警报。 | 若要解除锁定你的帐户，请联系你公司的 IT 支持人员。 |
| 激活 | 感谢你使用 Microsoft 登录验证系统。 请按井号键完成验证。 |
| 被拒身份验证重试 | 验证被拒。 |
| 重试（标准） | 感谢你使用 Microsoft 登录验证系统。 请按井号键完成验证。 |
| 问候语（标准） | 感谢你使用 Microsoft 登录验证系统。 请按井号键完成验证。 |
| 问候语 (PIN) | 感谢你使用 Microsoft 登录验证系统。 请先输入你的 PIN，然后再按井号键，以完成验证。 |
| 欺诈问候语 (PIN) | 感谢你使用 Microsoft 登录验证系统。  请先输入你的 PIN，然后再按井号键，以完成验证。 如果此次验证并非由你启动，则表明可能有人试图访问你的帐户。 请按 0 和井号键提交欺诈警报。 这将通知你公司的 IT 团队，并阻止进一步的验证尝试。 |
| 重试 (PIN) | 感谢你使用 Microsoft 登录验证系统。 请先输入你的 PIN，然后再按井号键，以完成验证。 |
| 数字之后的分机提示 | 如果你已经转到此分机号，请按井号键继续。 |
| 身份验证被拒绝 | 很抱歉，你暂时无法登录。 请稍后重试。 |
| 激活问候语（标准） | 感谢你使用 Microsoft 登录验证系统。 请按井号键完成验证。 |
| 激活重试（标准） | 感谢你使用 Microsoft 登录验证系统。 请按井号键完成验证。 |
| 激活问候语 (PIN) | 感谢你使用 Microsoft 登录验证系统。 请先输入你的 PIN，然后再按井号键，以完成验证。 |
| 数字之前的分机提示 | 感谢你使用 Microsoft 登录验证系统。 请将此呼叫转接到分机号… |

### <a name="set-up-a-custom-message"></a>设置自定义消息

若要使用自己的自定义消息，请完成以下步骤：

1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “电话呼叫设置”。   
1. 选择“添加问候语”。
1. 选择问候的“类型”，例如“问候语(标准)”或“身份验证成功”。 
1. 根据上一部分有关[自定义消息语言行为](#custom-message-language-behavior)中的说明选择“语言”。
1. 浏览并选择要上传的 *.mp3* 或 *.wav* 声音文件。
1. 准备就绪后，依次选择“添加”、“保存”。 

## <a name="mfa-service-settings"></a>MFA 服务设置

Azure AD 多重身份验证的应用密码、受信任的 IP、验证选项以及“记住多重身份验证”设置可以在服务设置中找到。 这些设置主要在传统门户而不是普通的 Azure AD 门户中完成。

可以从 Azure 门户访问服务设置，方法是：浏览到“Azure Active Directory” > “安全性” > “MFA” > “入门” > “配置” > “其他基于云的 MFA 设置”     。 此时将打开一个新窗口或选项卡，其中包含其他服务设置选项。

## <a name="trusted-ips"></a>受信任的 IP

对于从定义的 IP 地址范围登录的用户，Azure AD 多重身份验证的“受信任的 IP”功能会绕过向其显示多重身份验证提示的步骤。 可为本地环境设置受信任的 IP 范围，以便在用户处于这些位置之一时，不向其显示 Azure AD 多重身份验证提示。 Azure AD 多重身份验证的“受信任 IP”功能需要 Azure AD Premium P1 版本。 

> [!NOTE]
> 仅当使用 MFA 服务器时，受信任的 IP 才能包括专用 IP 范围。 对于基于云的 Azure AD 多重身份验证，只能使用公共 IP 地址范围。
>
> 只有 [命名位置（预览版）](../conditional-access/location-condition.md) 接口中支持 IPv6 范围。

如果你的组织部署 NPS 扩展，向本地应用程序提供 MFA，请注意源 IP 地址将始终显示为身份验证尝试流经的 NPS 服务器。

| Azure AD 租户类型 | 受信任的 IP 功能选项 |
|:--- |:--- |
| 托管 |**特定 IP 地址范围**：管理员指定 IP 地址范围，使从公司 Intranet 登录的用户可以绕过多重身份验证。 最多可以配置 50 个受信任的 IP 范围。|
| 联合 |**所有联合用户**：从组织内部登录的所有联合用户都可以绕过多重身份验证。 用户使用由 Active Directory 联合身份验证服务 (AD FS) 颁发的声明绕过验证。<br/>**特定 IP 地址范围**：管理员指定 IP 地址范围，使从公司 Intranet 登录的用户可以绕过多重身份验证。 |

受信任的 IP 绕过多重身份验证的功能仅在公司 Intranet 内部有效。 如果你选择了“所有联合用户”选项，而某个用户从公司 Intranet 外部登录，则该用户必须使用多重身份验证进行身份验证。 即使用户提供了 AD FS 声明，验证过程也相同。

### <a name="end-user-experience-inside-of-corpnet"></a>公司网络内的最终用户体验

如果禁用了受信任的 IP 功能，需要执行多重身份验证才能完成浏览器中的流程。 较旧的丰富客户端应用程序需要应用密码。

如果使用受信任的 IP，则无需执行多重身份验证即可完成浏览器中的流程。 如果用户尚未创建应用密码，则无需为早期的富客户端应用程序提供应用密码。 使用应用密码后，将始终要求提供该密码。

### <a name="end-user-experience-outside-corpnet"></a>公司网络外的最终用户体验

不管是否定义了受信任的 IP，都需要执行多重身份验证才能完成浏览器中的流程。 较旧的丰富客户端应用程序需要应用密码。

### <a name="enable-named-locations-by-using-conditional-access"></a>使用条件访问启用命名位置

可以遵循以下步骤使用条件访问规则来定义命名位置：

1. 在 Azure 门户中搜索并选择“Azure Active Directory”，然后浏览到“安全性” > “条件访问” > “命名位置”。   
1. 选择“新建位置”。
1. 输入位置的名称。
1. 选择“标记为受信任位置”。
1. 以 CIDR 表示法输入环境的 IP 范围，例如 *40.77.182.32/27*。
1. 选择“创建”。

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>使用条件访问启用受信任的 IP 功能

若要使用条件访问策略启用受信任的 IP，请完成以下步骤：

1. 在 Azure 门户中搜索并选择“Azure Active Directory”，然后浏览到“安全性” >  “条件访问” > “命名位置”。   
1. 选择“配置 MFA 受信任的 IP”。
1. 在“服务设置”页的“受信任的 IP”下，从以下两个选项中进行选择 ：

   * **对于联合用户从我的 Intranet 发起的请求**：若要选择此选项，请选中复选框。 所有从企业网络登录的联合用户均使用 AD FS 颁发的声明绕过多重身份验证。 请确保 AD FS 具有可将 intranet 声明添加到相应流量的规则。 如果规则不存在，请在 AD FS 中创建以下规则：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **对于来自特定公共 IP 范围的请求**：使用 CIDR 表示法在文本框中输入 IP 地址，以选择此选项。
      * 若要选择 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 范围内的 IP 地址，请使用如 **xxx.xxx.xxx.0/24** 的表示法。
      * 若要选择单个 IP 地址，请使用 **xxx.xxx.xxx.xxx/32** 这样的表示法。
      * 可输入至多 50 个 IP 地址范围。 从这些 IP 地址登录的用户将绕过多重身份验证。

1. 选择“保存”。

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>使用服务设置启用受信任的 IP 功能

如果你不想要使用条件访问策略来启用受信任的 IP，可使用以下步骤配置 Azure AD 多重身份验证的服务设置：

1. 在 Azure 门户中搜索并选择“Azure Active Directory”，然后选择“用户”。 
1. 选择“多重身份验证”。
1. 在“多重身份验证”下，选择“服务设置”。
1. 在“服务设置”页的“受信任的 IP”下，选择以下两个选项中的一个（或两个） ：

   * **对于我的 Intranet 上的联合用户发出的请求**：若要选择此选项，请选中复选框。 所有从企业网络登录的联合用户均使用 AD FS 颁发的声明绕过多重身份验证。 请确保 AD FS 具有可将 intranet 声明添加到相应流量的规则。 如果规则不存在，请在 AD FS 中创建以下规则：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **对于来自特定 IP 地址子网范围的请求**：使用 CIDR 表示法在文本框中输入 IP 地址，以选择此选项。
      * 若要选择 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 范围内的 IP 地址，请使用如 **xxx.xxx.xxx.0/24** 的表示法。
      * 若要选择单个 IP 地址，请使用 **xxx.xxx.xxx.xxx/32** 这样的表示法。
      * 可输入至多 50 个 IP 地址范围。 从这些 IP 地址登录的用户将绕过多重身份验证。

1. 选择“保存”。

## <a name="verification-methods"></a>验证方法

可以在服务设置门户中选择可供用户使用的验证方法。 用户为其帐户注册 Azure AD 多重身份验证时，将从你启用的选项中选择其首选验证方法。 [为我的帐户设置多重身份验证](../user-help/multi-factor-authentication-end-user-first-time.md)中提供了用户注册过程的指导。

可使用以下验证方法：

| 方法 | 说明 |
|:--- |:--- |
| 拨打电话 |拨打自动语音电话。 用户接听电话，并按电话键盘上的 # 进行身份验证。 此电话号码不会同步到本地 Active Directory。 |
| 向手机发送短信 |发送包含验证码的短信。 系统会提示用户在登录界面中输入验证代码。 此过程称为单向短信。 双向短信意味着用户必须短信回复一个特定代码。 已弃用双向短信，2018 年 11 月 14 日后不再受到支持。 管理员应为之前使用双向短信的用户启用其他方法。|
| 通过移动应用发送通知 |向手机或已注册设备发送推送通知。 用户将查看通知并选择 **验证** 来完成验证。 Microsoft Authenticator 应用可用于 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 移动应用或硬件标志提供的验证码 |Microsoft Authenticator 应用每隔 30 秒会生成一个新的 OATH 验证码。 用户将此验证码输入到登录界面中。 Microsoft Authenticator 应用可用于 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |

有关详细信息，请参阅 [Azure AD 中有哪些可用的身份验证和验证方法？](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>启用和禁用可选择验证方法

若要启用或禁用验证方法，请完成以下步骤：

1. 在 Azure 门户中搜索并选择“Azure Active Directory”，然后选择“用户”。 
1. 选择“多重身份验证”。
1. 在“多重身份验证”下，选择“服务设置”。
1. 在“服务设置”页上的“验证选项”下，选择/取消选择要向用户提供的方法。
1. 单击“ **保存**”。

## <a name="remember-multi-factor-authentication"></a>记住多重身份验证

通过“记住多重身份验证”功能，用户在使用多重身份验证成功登录到设备后，可在指定天数内绕过后续验证。 若要增强易用性并尽量减少用户必须在同一设备上执行 MFA 的次数，请选择 90 天或更长的持续时间。

> [!IMPORTANT]
> 请记住，如果帐户或设备遭到入侵，受信任的设备的多重身份验证可能影响安全性。 如果公司帐户受到安全威胁，或者受信任的设备已丢失或被盗，则应[撤销 MFA 会话](howto-mfa-userdevicesettings.md)。
>
> 还原操作会撤销所有设备的受信任状态，然后用户必须再次执行多重身份验证。 还可以指示用户按照[管理多重身份验证的设置](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)中所述在其自己的设备上还原多重身份验证。

### <a name="how-the-feature-works"></a>该功能的工作原理

在用户在登录时选择“X 天内不再询问”选项时，“记住多重身份验证”功能将在浏览器上设置持久性 Cookie。 在该 Cookie 过期之前，该浏览器不会再次提示用户进行多重身份验证。 如果用户在同一设备上打开不同浏览器或清除其 Cookie，会提示他们重新进行验证。

非浏览器应用程序上不会显示“X 天内不再询问”选项，无论该应用是否支持新式身份验证。 这些应用使用每小时提供新访问令牌的刷新令牌。 验证刷新令牌时，Azure AD 会检查上次多重身份验证是否发生在指定的天数内。

此功能可以减少 Web 应用的身份验证次数，而通常每次使用时都会提示验证。 如果配置的持续时间较短，该功能可以增加通常每隔 180 天发出提示的新式身份验证客户端的身份验证次数。 与条件访问策略结合使用时，还可能会增加身份验证的次数。

> [!IMPORTANT]
> 当用户通过 Azure 多重身份验证服务器或第三方多重身份验证解决方案对 AD FS 执行多重身份验证时，“记住多重身份验证”功能与 AD FS 的“使我保持登录”功能不兼容。 
>
> 如果你的用户在 AD FS 中选择“使我保持登录”同时将其设备标记为信任多重身份验证，则“记住多重身份验证”天数过期后不会自动验证该用户。 Azure AD 将请求全新的多重身份验证，但 AD FS 将返回包含原始多重身份验证声明和日期的令牌，而不是再次执行多重身份验证。 **此反应会引发 Azure AD 和 AD FS 之间的验证循环。**
>
> “记住多重身份验证”功能与 B2B 用户不兼容，在登录到受邀租户时，B2B 用户不会看到此功能。
>

### <a name="enable-remember-multi-factor-authentication"></a>启用“记住多重身份验证”

若要启用和配置可让用户记住其 MFA 状态并绕过提示的选项，请完成以下步骤：

1. 在 Azure 门户中搜索并选择“Azure Active Directory”，然后选择“用户”。 
1. 选择“多重身份验证”。
1. 在“多重身份验证”下，选择“服务设置”。
1. 在“服务设置”页上的“记住多重身份验证”下，选择“允许用户在其信任的设备上记住多重身份验证”选项。  
1. 设置允许受信任的设备绕过多重身份验证的天数。 为确保最佳用户体验，请将持续时间延长至 *90* 天或更长。
1. 选择“保存”。

### <a name="mark-a-device-as-trusted"></a>将设备标记为受信任的

启用“记住多重身份验证”功能后，用户可在登录时选择“不再询问”选项，将设备标记为受信任。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure AD 多重身份验证中可用的方法，请参阅 [Azure Active Directory 中有哪些可用的身份验证和验证方法？](concept-authentication-methods.md)
