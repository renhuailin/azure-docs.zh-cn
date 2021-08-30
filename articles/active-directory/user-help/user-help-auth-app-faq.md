---
title: 有关 Microsoft Authenticator 应用的问题和解答 - Azure AD
description: 有关 Microsoft 身份验证应用和双重验证的常见问题和解答 (FAQ)。
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 06/21/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f593141d425573888458b4b00858b17888c28e52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752487"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>有关 Microsoft Authenticator 应用的常见问题解答 (FAQ)

本文解答了有关 Microsoft Authenticator 应用的常见问题。 如果问题在此处没有解答，请访问 [Microsoft 验证器应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。

Microsoft Authenticator 应用替代了 Azure Authenticator 应用，建议使用 Azure AD 多重身份验证时使用该应用。 Microsoft Authenticator 应用可用于 [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 和 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="permission-to-access-your-location"></a>位置访问权限

问：我收到一个提示，要求我授予该应用程序访问我所在位置的权限。 为何会看到此信息？

答：如果你的 IT 管理员创建了一个策略，该策略要求你在允许访问特定资源之前共享你的 GPS 位置，你将会看到 Microsoft Authenticator 要求访问你所在位置的提示。 你需要每小时共享你的位置一次，以确保仍处于允许访问资源的国家/地区。

在 iOS 上，Microsoft 建议允许应用程序始终访问位置。 按照 iOS 提示授予该权限。 下面是每个权限级别所代表的含义：

- 使用应用程序时允许：如果选择此选项，系统将提示你选择其他两个选项。
- 始终允许（推荐）：如果你仍在访问受保护的资源，在接下来的 24 小时内，设备将每小时自动共享一次你的位置，因此你无需拿出手机并批准每小时的共享请求。
- 仅在使用时保留：如果你仍在访问受保护的资源，则每隔一小时需要拿出手机手动批准改请求。
- 允许一次：一小时内，如果你仍在访问资源，或下次尝试访问资源时，需要再次授予权限。 你将需要转到“设置”并手动启用权限。 
- 不允许：如果选择此选项，系统会阻止你访问该资源。 如果改变主意，你需要转到“设置”并手动启用权限。

在 Android 上，Microsoft 建议允许应用程序始终访问位置。 按照 Android 提示授予该权限。 下面是每个权限级别所代表的含义：

- 始终允许（推荐）：如果你仍在访问受保护的资源，在接下来的 24 小时内，设备将每小时自动共享一次你的位置，因此你无需拿出手机并批准每小时的共享请求。
- 仅在使用应用程序时允许：如果你仍在访问受保护的资源，则每隔一小时需要拿出手机手动批准改请求。
- 拒绝且不再询问：如果选择此选项，系统会阻止你访问该资源。

问：如何使用和存储我的位置信息？

答：Authenticator 应用程序将收集你的 GPS 信息以确定你所在的国家/地区。 国家/地区名称和位置坐标会发送回系统，以确定是否允许你访问受保护的资源。 系统会存储并将国家/地区名称报告给你的 IT 管理员，但 Microsoft 服务器从不保存或存储你的实际坐标。

### <a name="notification-blocks-sign-in"></a>通知阻止登录

**问**：我尝试登录，我需要选择应用的登录屏幕上显示的数字。 但是，来自 Authenticator 的通知提示阻止了登录屏幕。 我该怎么办？  

**答**：选择通知中的“隐藏”选项，这样可以看到登录屏幕和需要选择的数字。 5 秒后会再次出现提示，此时你可以选择正确的数字。

### <a name="registering-a-device"></a>注册设备

**问**：注册设备是否表示同意授权公司或服务访问我的设备？

**答**：注册设备可使你的设备访问组织的服务，但不允许组织访问你的设备。

### <a name="error-adding-account"></a>添加帐户时出错

**问**：我在尝试添加帐户时，收到一条错误消息，指出“你尝试添加的帐户暂时无效。 请联系管理员来解决此问题(唯一性验证)。” 应采取何种操作？

**答**：与管理员联系，让他们知道你因为唯一性验证问题无法向 Authenticator 添加帐户。 你需要提供登录用户名，使管理员能在你的组织中查找你。

### <a name="legacy-apns-support-deprecated"></a>已停止提供旧的 APNs 支持

**问**：Apple Push Notification 服务的旧式二进制接口已于 2020 年 11 月被弃用，我如何继续使用 Microsoft Authenticator/Phone Factor 登录？

**答**：[Apple 宣布弃用](https://developer.apple.com/news/?id=11042019a)使用 iOS 设备二进制接口的推送通知，例如 Phone Factor 使用的推送通知。 如果要继续接收推送通知，建议用户将 Authenticator 应用更新为最新版本。 在这期间，你可以采取一种变通办法：在 Authenticator 应用中手动检查通知。

### <a name="app-lock-feature"></a>应用锁定功能

**问**：什么是应用锁定，我如何使用它帮助我提高安全性？

**答**：应用锁定可以让你的一次性验证码、应用信息和应用设置更安全。 启用应用锁定后，你每次打开 Authenticator 时系统都会要求你使用设备 PIN 或生物识别信息进行身份验证。 应用锁定在你每次批准登录通知时提示你提供 PIN 或生物识别信息，从而确保你是唯一可以批准通知的人。 可以在“Authenticator 设置”页上启用或禁用应用锁定。 当你在设备上设置 PIN 或生物识别时，应用锁定默认处于启用状态。<br><br>遗憾的是，不能保证应用锁定会阻止他人访问 Authenticator。 因为设备注册可能会在 Authenticator 之外的其他地方进行，例如 Android 帐户设置或公司门户应用中。

### <a name="windows-mobile-retired"></a>Windows Mobile 已停用

**问**：我有一个 Windows Mobile 设备，它上面的 Microsoft Authenticator 已被弃用。 我还能继续使用该应用进行身份验证吗？

**答**：在 2020 年 7 月 15 日之后，所有使用 Windows Mobile 上的 Microsoft Authenticator 进行的身份验证都会被停用。 我们强烈建议你使用其他身份验证方法，以免帐户被锁定。<br>企业用户的备选方案包括：<br><ul><li>设置适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458) 的 Microsoft Authenticator。</li><li>[设置 SMS](multi-factor-authentication-setup-phone-number.md)，用于接收验证码。</li><li>设置电话号码，用于[接听电话以验证身份](multi-factor-authentication-setup-office-phone.md)。</li></ul><br>Microsoft 个人帐户用户的备选方案包括：<br><ul><li>设置适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458) 的 Microsoft Authenticator。</li><li>通过在 [Microsoft 帐户安全页面](https://account.microsoft.com/security/)中更新安全信息来设置备用登录方法（短信或电子邮件）。</li></ul>

### <a name="android-screenshots"></a>Android 屏幕截图

**问**：我可以在 Android Authenticator 上对我的一次性密码（OTP 码）进行屏幕截图吗？

**答**：从 Authenticator Android 的 6.2003.1704 版本开始，默认会在截取 Authenticator 屏幕图像时隐藏所有 OTP 码。 如果你希望在屏幕截图中看到 OTP 码，或者允许其他应用捕获 Authenticator 屏幕，也可以这样做。 只需在 Authenticator 中打开“屏幕捕获”设置，然后重启应用即可。

### <a name="delete-stored-data"></a>删除存储的数据

**问**：哪些数据是 Authenticator 以我的名义存储的，我如何删除这些数据？

**答**：Authenticator 应用收集三类信息：

- 你在添加帐户时提供的帐户信息。 添加帐户后，根据为帐户启用的功能，帐户数据可能会同步到应用。 可以通过删除帐户删除此数据。
- 诊断日志数据（在你通过应用顶部菜单中的“发送反馈”将日志发送到 Microsoft 之前，这些数据只保留在应用中）。 这些日志可以包含电子邮件地址、服务器地址或 IP 地址等个人数据。 也可以包含设备名和操作系统版本等设备数据。 收集的任何个人数据仅限有助于排查应用问题所需的信息。 随时都可以在应用中浏览这些日志文件，以查看收集的信息。 如果你发送日志文件，身份验证应用工程师只会将它们用于解决客户报告的问题。
- 非个人识别使用情况数据，例如“已启动添加帐户流/已成功添加了帐户”或者“通知已批准”。 这种数据是工程决策不可或缺的一部分。 你的使用情况有助于我们确定可以在哪些方面做出对你而言很重要的应用改进。 首次使用该应用时，你会看到这类数据收集的通知。 它告诉你可以在应用的“设置”页上关闭该功能 ****  。 你可以随时打开或关闭此设置。

### <a name="codes-in-the-app"></a>应用中的代码

**问**：应用中的代码有什么用途？

**答**：打开 Authenticator 时，你会看到添加的帐户以磁贴形式显示。 你的工作或学校帐户以及 Microsoft 个人帐户将有六位或八位数字显示在帐户的全屏视图中（可通过点击帐户磁贴访问）。 对于其他帐户，你可在应用的“帐户”页中看到六位或八位数字。<br>你需要将这些代码用作一次性密码来验证自己的身份。 使用用户名和密码登录后，需要键入与该帐户关联的验证码。 例如，如果你以 Katy 的身份登录到 Contoso 帐户，应点击该帐户磁贴，然后使用验证码 895823。 对于 Outlook 帐户，你可以执行相同的步骤。<br>点击 Contoso 帐户磁贴。<br>![Authenticator 应用中的帐户磁贴](media/user-help-auth-app-faq/katy-signin.png)<br>点击 Contoso 帐户磁贴后，验证码在全屏状态下显示。<br>![Authenticator 中的帐户磁贴中的验证码](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>倒计时计时器

**问**：为什么验证码旁边的数字一直在倒计时？

**答**：活动验证码的旁边可能会出现 30 秒的倒计时。 此计时器可防止使用同一代码登录两次。 与密码不同，你不需要记住此数字。 此机制确保只有有权访问你手机的人才知道验证码。

### <a name="grayed-account-tile"></a>灰显的帐户磁贴

**问**：帐户磁贴为何灰显？

**答**：某些组织要求 Authenticator 使用单一登录并保护组织资源。 在此情况下，该帐户不会用于双重验证，并且灰显或处于非活动状态。 此类帐户往往称为“代理”帐户。

### <a name="device-registration"></a>设备注册

**问**：什么是设备注册？

**答**：你的组织可能要求你注册设备以跟踪对受保护资源（如文件和应用）的访问。 他们还可能会启用条件访问，以减少对这些资源进行不必要的访问。 可以在“设置”中取消注册设备，但这可能会失去 Outlook 中电子邮件、OneDrive 中文件的访问权限，同时，可能无法使用手机登录。

### <a name="verification-codes-when-connected"></a>连接时的验证码

**问**：是否需要连接到 Internet 或网络才能获取和使用验证码？

**答**：无需连接到 Internet 或数据也可获取验证码，因此，登录并不需要手机服务。 此外，应用在关闭后会停止运行，因为不会耗尽电池。

### <a name="no-notifications-when-app-is-closed"></a>应用关闭时无通知

**问**：为什么我只在应用打开时收到通知？ 如果应用关闭，则无法收到通知。

**答**：如果接收的是通知而不是警报，则即使打开了铃声，也应该检查应用设置。 请确保在应用中启用声音或振动通知。 如果完全收不到通知，应检查是否存在以下情况：<ul><li>手机是否进入“免打扰”或“静音”模式？ 这些模式可能阻止应用发送通知。</li><li>是否能从其他应用收到通知？ 如果不能，原因可能是手机出现网络连接问题，或者 Android 或 Apple 的通知通道出现问题。 可以尝试通过电话设置来解决网络连接问题。 你可能需要联系服务提供商，请他们提供 Android 或 Apple 通知通道方面的帮助。</li><li>是否应用中的某些帐户可以收到通知，而其他帐户则不能？ 如果是，请从应用中删除有问题的帐户，然后再次添加并允许其发送通知，看看是否能解决问题。</li></ul>如果尝试了所有这些步骤但仍有问题，我们建议发送日志文件做诊断。 打开应用，转到应用的顶层菜单，然后选择“发送反馈” **** 。 然后，前往 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)，告诉 Microsoft 你遇到的问题以及尝试过的解决步骤。

### <a name="switch-to-push-notifications"></a>切换到推送通知

**问**：我使用的是应用中的验证码，如何切换到推送通知？

**答**：可以为你的工作或学校帐户（如果管理员允许）或者为 Microsoft 个人帐户设置通知。 通知不适用于 Google 或 Facebook 等第三方帐户。<br>若要将个人帐户切换到通知，必须在帐户中重新注册设备。 转到“添加帐户”，选择“个人 Microsoft 帐户”，然后使用你的用户名和密码进行登录。<br>对于工作或学校帐户，由组织决定是否允许一键式通知。

### <a name="notifications-for-other-accounts"></a>其他帐户的通知

**问**：通知是否适用于非 Microsoft 帐户？

**答**：不适用，通知只适用于 Microsoft 帐户和 Azure Active Directory 帐户。 如果工作单位或学校使用的是 Azure AD 帐户，他们可以关闭此功能。

### <a name="backup-and-recovery"></a>备份和恢复

**问**：我购买了新的设备，或者从备份还原了我的设备。 如何再次在 Authenticator 中设置我的帐户？

**答**：如果在旧设备上启用了“云备份”，则可以使用旧备份在你的新 iOS 或 Android 设备上恢复帐户凭据。 有关详细信息，请参阅[使用 Authenticator 备份和恢复帐户凭据](user-help-auth-app-backup-recovery.md)一文。

### <a name="lost-device"></a>设备丢失

**问**：我丢失了设备或者改用了新设备。 如何确保不会继续向旧设备发送通知？

**答**：将 Authenticator 添加到新设备不会自动从旧设备上删除该应用。 从旧设备中删除该应用并不足够。 必须从旧设备中删除该应用，并且告诉 Microsoft 或组织忽略并注销旧设备。<ul><li>**使用个人 Microsoft 帐户从设备中删除应用。** 转到[帐户安全](https://account.microsoft.com/security) 页的双重验证区域，选择关闭旧设备的验证。</li><li>**使用工作或学校 Microsoft 帐户从设备中删除应用。** 请转到[“我的应用”页面](https://myapps.microsoft.com/)或组织自定义门户的双重验证区域，关闭旧设备的验证。</li></ul>

### <a name="remove-account-from-app"></a>从应用中删除帐户

**问**：如何从应用中删除帐户？

**答**：点击要从应用中删除的帐户磁贴，即可全屏查看该帐户。 点击“删除帐户”，从应用中删除该帐户。<br>如果拥有已注册到组织的设备，可能需要完成一个额外的步骤才能删除帐户。 在这些设备上，Authenticator 自动注册为设备管理员。 如果想要彻底卸载应用，需受先在应用设置中取消注册应用。

### <a name="too-many-permissions"></a>过多的权限

**问**：应用为什么请求这么多的权限？

**答**：下面是可能需要的权限的完整列表以及这些权限在应用中的用法。 所见到的特定权限将取决于所持有的电话类型。<ul><li>**位置**。 有时，你的组织希望先知道你的位置，再允许你访问特定资源。 仅当组织有要求提供位置的策略时，应用才会请求此权限。</li><li>使用生物识别硬件。 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 应用要求你同意使用生物识别或面部识别，而不是输入 PIN。</li><li>**相机。** 在添加工作、学校或非 Microsoft 帐户时用于扫描 QR 码。</li><li>**联系人和电话。** 应用需要此权限才能在手机上搜索 Microsoft 工作或学校帐户，并将它们添加应用中。</li><li>**短信。** 用于确保你的电话号码与你首次使用 Microsoft 个人帐户登录时记录的号码匹配。 我们向你安装了该应用的手机发送一条短信，其中包含 6 到 8 位数字的验证码。 你不需要查找并输入此验证码，因为 Authenticator 会自动在短信中查找它。</li><li>**在其他应用上绘制。** 身份验证的通知也会显示在正运行的其他任何应用上。</li><li>**从 Internet 接收数据。** 必须使用此权限发送通知。</li><li>**防止手机休眠。** 如果向组织注册设备，组织可以更改手机上的这项策略。</li><li>**控制振动。** 可以选择在收到验证身份的通知时是否希望手机振动。</li><li>**使用指纹硬件。** 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 为了使过程更加简单，我们允许使用指纹而不是输入 PIN。</li><li> **查看网络连接。** 添加 Microsoft 帐户时，应用需要网络/Internet 连接。</li><li>**读取存储内容。** 仅当通过应用设置报告技术问题时才使用此权限。 会从存储中收集某些信息来诊断问题。</li><li>**完全网络访问。** 必须使用此权限发送通知以验证身份。</li><li>**启动时运行。** 如果重启手机，此权限可确保继续接收通知以验证身份。</li></ul>

### <a name="approve-requests-without-unlocking"></a>在不解锁的情况下批准请求

**问**：为何 Authenticator 允许在不解锁设备的情况下批准请求？

**答**：同意验证请求时无需解锁设备，只需证明带了手机。 双重验证要求提供两项证明：一件你知道的事和一件你拥有的物品。 只需知道密码。 拥有的物品是手机（已使用 Authenticator 进行设置，并已注册为多重身份验证证明）。因此，拥有手机和批准请求符合第二个身份验证因素的条件。

### <a name="activity-notifications"></a>活动通知

**问**：为什么会收到关于帐户活动的通知？

**答**：一旦 Microsoft 个人帐户发生变化，系统会立即将活动通知发送到 Authenticator，帮助提高安全性。 我们之前只通过电子邮件或短信发送这些通知。 有关这些活动通知的详细信息，请参阅[存在异常帐户登录时会发生什么](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)。 要更改接收通知的位置，请登录到帐户的[我们可以通过非关键帐户警报在何处与你联系](https://account.live.com/SecurityNotifications/Update)页面。

### <a name="one-time-passcodes"></a>一次性密码

**问**：我的一次性密码不起作用。 应采取何种操作？

**答**：请确保设备上的日期和时间正确，并且会自动同步。 如果日期和时间错误或不同步，该代码将不起作用。

### <a name="windows-10-mobile"></a>Windows 10 移动版

**问**：Windows 10 移动版操作系统已于 2019 年 12 月弃用。 是否也会弃用 Windows 移动版操作系统上的 Microsoft Authenticator？

**答**：2020 年 2 月 28 日之后，将停止对所有 Windows 移动版操作系统上的 Authenticator 的支持。 在上述日期之后，用户将无法收到任何新的应用更新。 2020 年 2 月 28 日之后，当前支持在所有 Windows 移动版操作系统上使用 Microsoft Authenticator 进行身份验证的 Microsoft 服务将开始停止提供其支持。 为了向 Microsoft 服务进行身份验证，我们强烈建议所有用户在此日期之前切换到备用身份验证机制。

### <a name="default-mail-app"></a>默认邮件应用

**问**：使用 iOS 附带的默认邮件应用登录到我的工作或学校帐户时，Authenticator 提示我输入安全验证信息。 在输入该信息并返回邮件应用后，我收到一个错误。 我该怎么办？

**答**：出现这种情况的原因很可能是，登录操作和邮件应用操作发生在两个不同的应用中，导致初始后台登录过程停止工作并发生故障。 若要尝试解决此问题，我们建议在登录到邮件应用时选择屏幕右下角的“Safari”图标。 通过移动到 Safari，整个登录过程将在单个应用中进行，从而使你能够成功登录到应用。

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**问**：我在使用 Apple Watch watchOS 7 时为什么会遇到问题？

**答**：有时，无法批准也无法拒绝 watchOS 7 上的会话，并出现错误消息“未能与手机通信。 请确保在未来请求的过程中 Watch 屏幕保持唤醒状态。 有关详细信息，请查看常见问题解答。”。 启用应用锁或需要号码匹配时，通知出现已知问题，我们正在与 Apple 合作来解决此问题。 同时，应改为在你的手机上批准所有需要 Microsoft Authenticator watchOS 应用的通知。

### <a name="signing-into-an-ios-app"></a>登录 iOS 应用

**问**：我尝试登录应用 iOS，我需要在 Authenticator 应用上批准一则通知。 但当我返回 iOS 应用时，软件卡住了。 我该怎么办？

**答**：这是 iOS 13 及更高版本中的一个已知问题。 请联系支持管理员寻求帮助，并提供以下详细信息：`Use Azure MFA, not MFA server.`

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch 不显示帐户

**问**：在 Apple Watch 中打开 Authenticator 时，为何不会显示我的所有帐户？

**答**：Authenticator 仅支持在 Apple Watch 伴侣应用上为 Microsoft 个人帐户或者工作或学校帐户使用推送通知。 对于其他帐户（例如 Google 或 Facebook），必须在手机上打开 Authenticator 应用才能查看验证码。

### <a name="apple-watch-notifications"></a>Apple Watch 通知

**问**：为何无法在 Apple Watch 上批准或拒绝通知？

**答**：首先，请确保已在 iPhone 上将 Authenticator 升级到 6.0.0 或更高版本。 然后，在 Apple Watch 上打开 Microsoft Authenticator 伴侣应用，并查看其下面带有“设置”按钮的所有帐户。  完成设置过程才能批准这些帐户的通知。

### <a name="apple-watch-communication-error"></a>Apple Watch 通信错误

**问**：Apple Watch 和我的手机之间出现通信错误。 我可以做什么来排除故障？

**答**：当 Watch 屏幕在完成与手机通信之前进入睡眠状态时会发生此错误。<br><b>如果此错误发生在安装过程中：</b><br>再次尝试运行安装程序，在完成之前，请确保 Watch 处于待机状态。 同时打开手机上的应用，并响应任何出现的提示。<br>如果手机与 Watch 仍无法通信，可以尝试以下操作：<ol><li>强制退出 Microsoft Authenticator 手机应用，并在 iPhone 上再次打开它。</li><li>在 Apple Watch 上强制退出伴侣应用。<ol><li> 在 Watch 上打开 Microsoft Authenticator 伴侣应用</li><li>按住边侧按钮，直到“关机”屏幕出现。 </li><li>松开边侧按钮，并按住 Digital Crown 强制退出活动的应用。</li></ol></li><li>关闭手机和 Watch 的蓝牙与 Wi-Fi，然后重新打开。</li><li>重启 iPhone 和 Watch。</li></ol><b>如果此错误发生在尝试批准通知时：</b><br>下次试图批准 Apple Watch 上的通知时，在请求完成并听到表示成功的提示音之前，请保持屏幕处于待机状态。

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch 伴侣应用未同步

**问**：为何适用于 Apple Watch 的 Microsoft Authenticator 伴侣应用在手表上不同步或显示？

**答**：如果该应用未显示在 Watch 上，请尝试以下操作： <ol><li>确保 Watch 运行 watchOS 4.0 或更高版本。</li><li>再次同步 Watch。</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch 伴侣应用故障

**问**：我的 Apple Watch 伴侣应用出现故障。 是否可以向你们发送崩溃日志以便调查？

**答**：首先，请务必选择与我们共享分析数据。 如果你是 TestFlight 用户，则已注册。 否则，可以转到“设置”>“隐私”>“分析”，并同时选择“共享 iPhone 和 Watch 分析”和“与应用开发人员共享”选项。  <br>注册后，可以尝试重现崩溃，因此自动将崩溃日志发送给我们进行调查。 但是，如果无法重现崩溃，可以手动复制日志文件并将其发送给我们。<ol><li>在手机上打开 Watch 应用，转到“设置”>“通用”，然后单击“复制 Watch 分析数据”。 </li><li>在“设置”>“隐私”>“分析”>“分析数据”下找到相应的崩溃信息，然后手动复制整个文本。 </li><li>在你的手机上打开 Authenticator，并将复制的文本粘贴到“发送反馈”页上的“遇到了问题?”下的“描述你遇到的问题”框中 ****  **** 。 </li></ol>

## <a name="autofill-with-authenticator"></a>Authenticator 自动填充

**问**：什么是 Authenticator 自动填充？

**答**：Authenticator 应用现在可以安全地在你通过手机访问的应用和网站上存储和自动填充密码。 你可以使用自动填充功能在 iOS 和 Android 设备上同步和自动填充密码。 在手机上将 Authenticator 应用设置为自动填充提供程序后，当你在网站上或在应用登录页面中输入密码时，它可以保存密码。 这些密码会被作为[你的 Microsoft 个人帐户](https://account.microsoft.com/account)的一部分保存，在你使用该帐户登录 Microsoft Edge 时也可用。

**问**：Authenticator 可以自动填充哪些信息？

**答**：Authenticator 可以自动填充你通过手机访问的网站和应用的用户名和密码。

**问**：如何在手机上的 Authenticator 中开启密码自动填充功能？

**答:** ：执行以下步骤：

1. 打开 Authenticator 应用。
1. 在 Authenticator 的“密码”选项卡上，选择“使用 Microsoft 登录”，并使用[你的 Microsoft 帐户](https://account.microsoft.com/account)登录 。 此功能目前仅支持 Microsoft 个人帐户，尚不支持工作或学校帐户。

**问**：如何在手机上将 Authenticator 设置为默认的自动填充提供程序？

**答:** ：执行以下步骤：

1. 打开 Authenticator 应用。
1. 在应用内的“密码”选项卡上，选择“使用 Microsoft 登录”，并使用[你的 Microsoft 帐户](https://account.microsoft.com/account)登录 。
1. 执行下列操作之一：

   - 在 iOS 的“设置”下的“自动填充设置”部分中选择“如何打开自动填充”，了解如何将 Authenticator 设置为默认自动填充提供程序 。
   - 在 Android 的“设置”下的“自动填充设置”部分选择“设置为自动填充提供程序” 。

**问**：如果我无法在“设置”中使用“自动填充”该怎么办？

**答**：如果“自动填充”在 Authenticator 中不可用，可能是因为你的组织或帐户类型尚未获得使用自动填充功能的许可。 你可以在未添加工作或学校帐户的设备上使用此功能。 若要详细了解如何使组织能够使用“自动填充”功能，请参阅 [IT 管理员的自动填充](#autofill-for-it-admins)。

**问**：如何停止密码同步？

**答**：如果要在 Authenticator 应用中停止同步密码，请打开“设置” > “自动填充设置” > “同步帐户”  。 在下一个屏幕上，可以选择“停止同步并删除所有自动填充数据”。 这会删除设备中的密码和其他自动填充数据。 删除自动填充数据不影响多重身份验证。

**问**：Authenticator 应用如何保护我的密码？

**答**：Authenticator 应用已为多重身份验证和帐户管理提供高级别的安全性，也将同样高的安全标准扩展到了密码管理方面。

- **Authenticator 应用需要强身份验证**：登录 Authenticator 需要额外的一层验证。 也就是说，即使有人知道你的 Microsoft 帐户密码，Authenticator 应用中的密码也会受到保护。
- **自动填充数据功能受生物识别和密码保护**：在应用或站点上自动填充密码之前，Authenticator 需要生物识别或设备密码。 这有助于增强安全性，即使他人有权访问你的设备，他们也无法填充或查看你的密码，因为他们无法提供生物识别或设备 PIN 输入。 而且，除非用户提供生物识别或 PIN（即使在应用设置中关闭了应用锁定），否则他无法打开“密码”页面。
- **设备上的加密密码**：设备上的密码是加密的，永不存储加密/解密密钥，仅根据需要生成这些密钥。 仅在用户需要时才会对密码进行解密，也就是在自动填充或用户想查看密码时才会解密，这两种情况都需要生物识别或 PIN 码。
- **云和网络安全**：仅在云上的密码到达设备时才对它们进行加密和解密。 密码通过受 SSL 保护的 HTTPS 连接同步，这有助于防止攻击者窃听正在同步的敏感数据。 我们还确保使用加密哈希函数（具体而言为基于哈希的消息身份验证代码）检查通过网络同步的数据的状态。

## <a name="autofill-for-it-admins"></a>IT 管理员的自动填充

**问**：我的员工或学生可以在 Authenticator 应用中使用密码自动填充功能吗？

**答**：可以。即使在 Authenticator 应用中添加了工作或学校帐户，[Microsoft 个人帐户](https://go.microsoft.com/fwlink/?linkid=2144423)的自动填充功能现在也适用于大多数企业用户。 你可以填写一张用于允许或拒绝组织使用自动填充功能的表单，并[将其发送给 Authenticator 团队](https://aka.ms/ConfigureAutofillInAuthenticator)。 自动填充功能当前不适用于工作或学校帐户。

**问**：会自动同步我的用户的工作或学校帐户密码吗？

**答**：否。 密码自动填充不会同步用户的工作或学校帐户密码。 用户访问站点或应用时，Authenticator 将提供为该站点或应用保存密码的功能，而且仅在用户选择保存密码时进行保存。
  
**问**：我可以只将组织中的特定用户加入“自动填充”的允许列表？

**答**：否。 目前，企业只能要么为全部员工都启用密码自动填充功能，要么都不启用。

**问**：如果我的员工或学生有多个工作或学校帐户怎么办？ 例如，我的员工在 Microsoft Authenticator 中有多个企业或学校的帐户。

**答**：需要将添加到 Authenticator 应用中的所有企业或学校全部加入 Authenticator 中的“自动填充”允许列表，以便应用所有者能够使用。 一个例外的情况是：员工或学生将其工作或学校帐户作为[外部或第三方帐户](user-help-auth-app-add-non-ms-account.md)添加到基于云的多重身份验证中。

## <a name="next-steps"></a>后续步骤

- 如果在获取个人 Microsoft 帐户的验证码时遇到问题，请参阅 [Microsoft 帐户安全信息和验证码](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的“排查验证码问题”部分。

- 如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- 如需有关安全信息的详细信息，请参阅[安全信息（预览版）概述](./security-info-setup-signin.md)

- 如果此文档没有回答问题，请告知我们。 转到 [Microsoft 验证器应用论坛](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) 发布问题并从社区获取帮助或者在此页面上留言，我们会尽快解答相关问题。
