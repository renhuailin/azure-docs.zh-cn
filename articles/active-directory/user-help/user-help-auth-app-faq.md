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
ms.date: 01/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2be1c7f76055cad1cf8b51938a65c7bbe6b2917e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881072"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>常见问题 (常见问题解答) Microsoft Authenticator 应用

本文解答了有关 Microsoft Authenticator 应用的常见问题。 如果没有看到所提问题的答案，请转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。

Microsoft Authenticator 应用取代了 Azure Authenticator 应用，在使用 Azure AD 多重身份验证时，建议使用此应用。 Microsoft Authenticator 应用可用于 [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 和 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="registering-a-device"></a>注册设备

**问**：注册的设备是否允许公司或服务访问我的设备？

**答**：注册设备将使你的设备能够访问你的组织的服务，而不允许你的组织访问你的设备。

### <a name="error-adding-account"></a>添加帐户时出错

**问**：如果我尝试添加我的帐户，会收到一条错误消息，指出 "当前要添加的帐户无效。 请与管理员联系以解决此问题 () 的唯一性验证。 " 应采取何种操作？

**答**：与管理员联系，让他们知道因为唯一性验证问题而无法将帐户添加到身份验证器。 你需要提供登录用户名，以便管理员能够在你的组织中查找。

### <a name="legacy-apns-support-deprecated"></a>旧 APNs 支持已弃用

**问**：由于 Apple 推送通知服务的旧二进制接口在11月2020中被弃用，如何继续使用 Microsoft Authenticator/电话系数进行登录？

**答**： [Apple 宣布弃用](https://developer.apple.com/news/?id=11042019a) 用于 iOS 设备的二进制接口的推送通知，如电话系数所使用的接口。 若要继续接收推送通知，建议用户将其验证器应用更新到最新版本的应用。 同时，你可以通过在验证器应用中手动检查通知来解决此情况。

### <a name="app-lock-feature"></a>应用锁定功能

**问**：什么是应用锁定，如何使用它来帮助使我更安全？

**答**：应用锁定有助于使你的一次性验证代码、应用信息和应用设置更安全。 启用应用锁定后，每次打开身份验证器时，系统都会要求使用设备 PIN 或生物识别进行身份验证。 应用锁定还有助于确保你是唯一可以通过在批准登录通知时提示你提供 PIN 或生物识别来批准通知的人。 可以在 "验证器设置" 页上打开或关闭应用锁定。 默认情况下，当你在设备上设置 PIN 或生物识别时，应用锁定处于开启状态。<br><br>遗憾的是，不能保证应用锁定会阻止他人访问身份验证器。 这是因为设备注册可能发生在验证器之外的其他位置中，如 Android 帐户设置或公司门户应用。

### <a name="windows-mobile-retired"></a>Windows Mobile 已停用

**问**：我有一个 windows mobile 设备，并且已不推荐使用 windows mobile 上的 Microsoft Authenticator。 能否继续使用该应用进行身份验证？

**答**：在2020年7月15日之后，使用 Windows Mobile 上的 Microsoft Authenticator 的所有身份验证都将停用。 强烈建议使用备用身份验证方法，避免被锁定。<br>适用于企业用户的其他选项包括：<br><ul><li>设置适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft Authenticator。</li><li>[设置 SMS](multi-factor-authentication-setup-phone-number.md) 以接收验证码。</li><li>设置电话号码以接收电话号码来 [验证其身份](multi-factor-authentication-setup-office-phone.md)。</li></ul><br>适用于个人 Microsoft 帐户用户的其他选项包括：<br><ul><li>设置适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft Authenticator。</li><li>通过从 " [Microsoft 帐户安全性" 页](https://account.microsoft.com/security/)更新安全信息，设置 (短信或电子邮件) 的备用登录方法。</li></ul>

### <a name="android-screenshots"></a>Android 屏幕快照

**问**：我是否能在 Android 验证器上获取我的一次性密码 (OTP) 代码的屏幕截图？

**答**：从6.2003.1704 的 release 版开始，默认情况下所有 OTP 代码都会在验证器的屏幕截图时隐藏。 如果要在屏幕截图中查看 OTP 代码或允许其他应用捕获验证器屏幕，可以。 只需在验证器中打开 **屏幕捕获** 设置，然后重新启动应用程序。

### <a name="delete-stored-data"></a>删除存储的数据

**问**：身份验证器将代表什么数据，如何删除它？

**答**：验证器应用将收集三种类型的信息：<ul><li>你在添加帐户时提供的帐户信息。 可以通过删除帐户删除此数据。</li><li>诊断日志数据，在选择该应用的“帮助”菜单中的“发送日志”将日志发送到 Microsoft 之前，该数据只会存在于和保留在该应用中 。 这些日志可以包含个人数据，例如电子邮件地址、服务器地址或 IP 地址。 它们还可以包含设备数据，例如设备名称和操作系统版本。 收集的任何个人数据仅限于帮助排查应用问题所需的信息。 你可以随时在应用程序中浏览这些日志文件，以查看收集的信息。 如果发送日志文件，则身份验证应用工程师仅将其用于排查客户报告的问题。</li><li>非个人识别使用情况数据，例如“已启动添加帐户流/已成功添加了帐户”或者“通知已批准”。 此数据是我们的工程决策不可或缺的一部分。 你的使用情况可帮助我们确定如何以对你很重要的方式来改进应用。 首次使用该应用时，将看到此数据收集的通知。 然后，它会通知你可以在应用的 " **设置** " 页上关闭该功能。 您可以随时打开或关闭此设置。</li></ul>

### <a name="codes-in-the-app"></a>应用中的代码

**问**：应用中的代码有哪些用途？

**答**：打开身份验证器时，你会看到添加的帐户作为磁贴。 你的工作或学校帐户以及你的个人 Microsoft 帐户将有六位或八位数字， (通过点击 "帐户" 磁贴) 来访问该帐户的全屏视图。 对于其他帐户，你将在应用的 " **帐户** " 页中看到六位或八位数字。<br>你将使用这些代码作为一次性密码来验证你是否为你所说的。 使用用户名和密码登录后，需要键入与该帐户关联的验证码。 例如，如果要 Katy 登录到 Contoso 帐户，请点击 "帐户" 磁贴，并使用验证码895823。 对于 Outlook 帐户，你可以执行相同的步骤。<br>点击 "Contoso 帐户" 磁贴。<br>![验证器应用中的帐户磁贴](media/user-help-auth-app-faq/katy-signin.png)<br>点击 Contoso 帐户磁贴后，验证代码在全屏显示。<br>![验证器中的帐户磁贴中的验证码](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>倒计时计时器

**问**：为什么代码旁边的数字会不断计数？

**答**：你可能会看到一个处于活动状态的验证代码旁边的30秒计时器。 此计时器可防止使用同一代码登录两次。 与密码不同，你不需要记住此数字。 此机制确保只有有权访问你手机的人才知道验证码。

### <a name="grayed-account-tile"></a>灰显的帐户磁贴

**问**：为什么我的帐户磁贴灰？

**答**：某些组织要求使用身份验证器来使用单一登录，并保护组织资源。 在此情况下，该帐户不会用于双重验证，并且灰显或处于非活动状态。 此类帐户往往称为“代理”帐户。

### <a name="device-registration"></a>设备注册

**问**：什么是设备注册？
**答**：你的组织可能要求你注册设备以跟踪对受保护资源（如文件和应用）的访问。 他们还可能会启用条件访问，以减少对这些资源进行不必要的访问。 可以在“设置”中取消注册设备，但这可能会失去 Outlook 中电子邮件、OneDrive 中文件的访问权限，同时，可能无法使用手机登录。

### <a name="verification-codes-when-connected"></a>连接时的验证码

**问**：我是否需要连接到 Internet 或我的网络以获取并使用验证码？

**答**：这些代码不要求你在 Internet 上或连接到数据，因此无需电话服务即可登录。 此外，应用在关闭后会停止运行，因为不会耗尽电池。

### <a name="no-notifications-when-app-is-closed"></a>应用关闭时无通知

**问**：为何我在打开应用程序时才会收到通知？ 应用关闭后，我将不会收到通知。

**答**：如果你收到通知，但不发出警报，则应检查应用设置。 请确保在应用中启用声音或振动通知。 如果根本没有收到通知，应检查以下条件：<ul><li>手机是否进入“免打扰”或“静音”模式？ 这些模式可能阻止应用发送通知。</li><li>是否能从其他应用收到通知？ 如果不能，原因可能是手机出现网络连接问题，或者 Android 或 Apple 的通知通道出现问题。 可以尝试通过电话设置来解析网络连接。 可能需要与服务提供商联系，以帮助提供 Android 或 Apple 通知通道。</li><li>是否应用中的某些帐户可以收到通知，而其他帐户则不能？ 如果是，请从应用中删除有问题的帐户，然后再次添加并允许其发送通知，看看是否能解决问题。</li></ul>如果尝试了所有这些步骤但仍有问题，我们建议发送日志文件做诊断。 打开应用，转到“帮助”并选择“发送日志”。   完成此操作后，请前往 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) ，告诉我们你遇到的问题以及你尝试执行的步骤。

### <a name="switch-to-push-notifications"></a>切换到推送通知

**问**：我使用的是应用程序中的验证码，但如何切换到推送通知？

**答**：你可以为工作或学校帐户设置通知 (如果管理员允许) 或你的个人 Microsoft 帐户，则为。 对于第三方帐户（如 Google 或 Facebook），通知将不起作用。<br>若要将你的个人帐户切换到通知，你必须使用该帐户重新注册设备。 转到“添加帐户”，选择“个人 Microsoft 帐户”，然后使用你的用户名和密码进行登录。<br>对于工作或学校帐户，组织决定是否允许一键式通知。

### <a name="notifications-for-other-accounts"></a>其他帐户的通知

**问**：通知是否适用于非 Microsoft 帐户？

**答**：不可以，通知仅适用于 Microsoft 帐户和 Azure Active Directory 帐户。 如果你的工作或学校使用 Azure AD 帐户，则他们可以关闭此功能。

### <a name="backup-and-recovery"></a>备份和恢复

**问**：我有一个新设备或从备份还原了我的设备。 如何实现在身份验证器中再次设置我的帐户？

**答**：如果在旧设备上启用了 **云备份** ，则可以使用旧的备份在新的 iOS 或 Android 设备上恢复帐户凭据。 有关详细信息，请参阅在验证器中 [备份和恢复帐户凭据](user-help-auth-app-backup-recovery.md) 一文。

### <a name="lost-device"></a>设备丢失

**问**：我的设备丢失或移到新设备上。 如何确保不会继续向旧设备发送通知？

**答**：将身份验证器添加到新设备不会自动从旧设备中删除该应用。 从旧设备中删除该应用并不足够。 您必须从旧设备中删除该应用程序，并告诉 Microsoft 或您的组织忘记并取消注册旧设备。<ul><li>**使用个人 Microsoft 帐户从设备中删除应用。** 转到[帐户安全](https://account.microsoft.com/security) 页的双重验证区域，选择关闭旧设备的验证。</li><li>**使用工作或学校 Microsoft 帐户从设备中删除应用。** 转到 [MyApps 页面](https://myapps.microsoft.com/) 或组织的自定义门户的双重验证区域，以关闭旧设备的验证。</li></ul>

### <a name="remove-account-from-app"></a>从应用中删除帐户

**问**：如何实现从应用中删除帐户？

**答**：单击要从应用中删除的帐户的 "帐户" 磁贴，以查看帐户 "全屏"。 点击 " **删除帐户** " 以从应用中删除帐户。<br>如果你的设备已注册到组织，你可能需要额外的步骤来删除帐户。 在这些设备上，验证器自动注册为设备管理员。 如果想要彻底卸载应用，需受先在应用设置中取消注册应用。

### <a name="too-many-permissions"></a>权限太多

**问**：为什么应用程序请求如此多的权限？

**答**：下面是可能需要的权限的完整列表，以及应用的使用方式。 所见到的特定权限将取决于所持有的电话类型。<ul><li>**位置**。 有时，你的组织希望在允许访问特定资源之前了解你的位置。 仅当你的组织有需要位置的策略时，应用才会请求此权限。</li><li>使用生物识别硬件。 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 应用要求你同意使用生物识别或面部识别，而不是输入 PIN。</li><li>**相机。** 在添加工作、学校或非 Microsoft 帐户时用于扫描 QR 码。</li><li>**联系人和电话。** 应用需要此权限才能在手机上搜索工作或学校 Microsoft 帐户，并将其添加到应用。</li><li>**短信。** 用于确保首次使用个人 Microsoft 帐户登录时，电话号码与记录中的数字匹配。 我们向手机发送一条短信，其中包含6-8 位验证码。 不需要查找此代码并输入它，因为身份验证器会自动在短信中查找它。</li><li>**在其他应用上绘制。** 验证身份的通知也会显示在任何其他正在运行的应用上。</li><li>**从 Internet 接收数据。** 必须使用此权限发送通知。</li><li>**防止手机休眠。** 如果向组织注册设备，组织可以更改手机上的这项策略。</li><li>**控制振动。** 可以选择在收到验证身份的通知时是否希望手机振动。</li><li>**使用指纹硬件。** 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 为了使过程更加简单，我们允许使用指纹而不是输入 PIN。</li><li> **查看网络连接。** 添加 Microsoft 帐户时，应用需要网络/Internet 连接。</li><li>**读取存储内容。** 仅当通过应用设置报告技术问题时才使用此权限。 会从存储中收集某些信息来诊断问题。</li><li>**完全网络访问。** 必须使用此权限发送通知以验证身份。</li><li>**启动时运行。** 如果重启手机，此权限可确保继续接收通知以验证身份。</li></ul>

### <a name="approve-requests-without-unlocking"></a>无需解锁即可批准请求

**问**：为什么身份验证器允许你在不解锁设备的情况下批准请求？

**答**：无需解锁设备即可批准验证请求，因为你只需证明自己的电话就可以。 双重验证需要证明这两件事：你知道的事情，以及你拥有的东西。 只需知道密码。 你使用的是手机 (使用身份验证器进行设置，并将其注册为多重身份验证证明。 ) 因此，让手机和批准请求符合第二个身份验证因素的标准。

### <a name="activity-notifications"></a>活动通知

**问**：为什么我会收到有关我的帐户活动的通知？

**答**：只要更改了个人 Microsoft 帐户，就会立即将活动通知发送到身份验证器，从而使你更安全。 我们以前仅通过电子邮件和短信发送了这些通知。 有关这些活动通知的详细信息，请参阅[存在异常帐户登录时会发生什么](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)。 要更改接收通知的位置，请登录到帐户的[我们可以通过非关键帐户警报在何处与你联系](https://account.live.com/SecurityNotifications/Update)页面。

### <a name="one-time-passcodes"></a>一次性密码

**问**：我的一次性密码不起作用。 应采取何种操作？

**答**：请确保设备上的日期和时间正确，并且已自动同步。 如果日期和时间错误或不同步，该代码将不起作用。

### <a name="windows-10-mobile"></a>Windows 10 移动版

**问**： Windows 10 移动版操作系统不推荐使用12月2019。 是否也会弃用 Windows 移动版操作系统上的 Microsoft Authenticator？

**答**：在2020年2月28日之后，将不支持所有 Windows Mobile 操作系统上的身份验证器。 在上述日期之后，用户将无法收到任何新的应用更新。 2020 年 2 月 28 日之后，当前支持在所有 Windows 移动版操作系统上使用 Microsoft Authenticator 进行身份验证的 Microsoft 服务将开始停止提供其支持。 为了向 Microsoft 服务进行身份验证，我们强烈建议所有用户在此日期之前切换到备用身份验证机制。

### <a name="default-mail-app"></a>默认邮件应用

**问**：在使用 iOS 随附的默认邮件应用程序登录到 "我的工作" 或 "学校" 帐户时，会收到由身份验证程序提示我输入安全验证信息的提示。 在输入该信息并返回邮件应用后，我收到一个错误。 我该怎么办？

**答**：这种情况最有可能发生，因为你的登录和邮件应用程序发生在两个不同的应用程序中，导致初始后台登录过程停止工作并发生故障。 若要尝试解决此问题，我们建议在登录到邮件应用时选择屏幕右下角的“Safari”图标。 通过移动到 Safari，整个登录过程将在单个应用中进行，从而使你能够成功登录到应用。

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**问**：为什么在 watchOS 7 上出现 Apple Watch 问题？

**答**：在 watchOS 7 上批准通知时出现问题，我们正在与 Apple 合作以解决此问题。 同时，需要在你的手机上批准需要 Microsoft Authenticator watchOS 应用的任何通知。

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch 不显示帐户

**问**：在我的 Apple Watch 上打开身份验证器时，为何我的所有帐户都不显示？

**答**：验证器仅支持 Apple Watch 配套应用上包含推送通知的 Microsoft 个人或学校或工作帐户。 对于其他帐户（如 Google 或 Facebook），必须打开手机上的验证器应用以查看验证代码。

### <a name="apple-watch-notifications"></a>Apple Watch 通知

**问**：为什么我无法批准或拒绝 Apple Watch 上的通知？

**答**：首先，请确保已在 iPhone 上升级到6.0.0 或更高版本的身份验证器。 然后，在 Apple Watch 上打开 Microsoft Authenticator 伴侣应用，并查看其下面带有“设置”按钮的所有帐户。  完成设置过程以批准这些帐户的通知。

### <a name="apple-watch-communication-error"></a>Apple Watch 通信错误

**问**：我在 "Apple Watch" 和 "我的手机" 之间收到通信错误。 我可以做什么来排除故障？

**答**：当你的手表屏幕进入睡眠状态才能完成与手机的通信之前，会发生此错误。<br><b>如果在安装过程中发生错误：</b><br>再次尝试运行安装程序，在完成之前，请确保 Watch 处于待机状态。 同时打开手机上的应用，并响应任何出现的提示。<br>如果你的手机和手表仍未通信，你可以尝试以下操作：<ol><li>强制退出 Microsoft Authenticator 手机应用，并在 iPhone 上再次打开它。</li><li>在 Apple Watch 上强制退出伴侣应用。<ol><li> 在 Watch 上打开 Microsoft Authenticator 伴侣应用</li><li>按住边侧按钮，直到“关机”屏幕出现。 </li><li>松开边侧按钮，并按住 Digital Crown 强制退出活动的应用。</li></ol></li><li>关闭手机和 Watch 的蓝牙与 Wi-Fi，然后重新打开。</li><li>重启 iPhone 和 Watch。</li></ol><b>如果尝试批准通知时出现错误：</b><br>下次试图批准 Apple Watch 上的通知时，在请求完成并听到表示成功的提示音之前，请保持屏幕处于待机状态。

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch 辅助应用未同步

**问**：为什么 Microsoft Authenticator 的应用程序 Apple Watch 在我的手表中同步或显示？

**答**：如果应用未显示在手表上，请尝试以下操作： <ol><li>确保 Watch 运行 watchOS 4.0 或更高版本。</li><li>再次同步 Watch。</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch 的伴随应用崩溃

**问**：我的 Apple Watch 伴生应用崩溃。 是否可以向你们发送崩溃日志以便调查？

**答**：你首先需要确保你已选择与我们共享你的分析。 如果你是 TestFlight 用户，则已注册。 否则，可以转到“设置”>“隐私”>“分析”，并同时选择“共享 iPhone 和 Watch 分析”和“与应用开发人员共享”选项。  <br>注册后，可以尝试重现崩溃，因此自动将崩溃日志发送给我们进行调查。 但是，如果无法重现崩溃，可以手动复制日志文件并将其发送给我们。<ol><li>在手机上打开 Watch 应用，转到“设置”>“通用”，然后单击“复制 Watch 分析数据”。 </li><li>在“设置”>“隐私”>“分析”>“分析数据”下找到相应的崩溃信息，然后手动复制整个文本。 </li><li>在手机上打开身份验证器，并将复制的文本粘贴到 "**发送日志**" 页上的 "**与应用程序开发人员共享**" 文本框中。</li></ol>

## <a name="autofill-for-consumers"></a>使用者自动填充

**问**：验证器中的自动填充是什么？

**答**：验证器应用现在会安全地存储和会自动填充在手机上访问的应用和网站上的密码。 你可以使用自动填充在 iOS 和 Android 设备上同步和自动填充你的密码。 在您的手机上将身份验证器应用程序设置为自动填充提供程序后，在站点或应用登录页输入密码时，它会保存密码。 密码将作为 [Microsoft 帐户](https://account.microsoft.com/account) 的一部分保存，并且在你使用 Microsoft 帐户登录 Microsoft Edge 时也可用。

**问**：身份验证器可以自动填充哪些信息？

**答**：验证器可以自动填充你在手机上访问的站点和应用上的用户名和密码。

**问**：如何实现在我的手机上打开身份验证器中的密码自动填充功能吗？

**答**：请执行以下步骤：

1. 打开验证器应用。
1. 在 " **Beta**" 下的 "**设置**" 中，打开自动 **填充**。
1. 在验证器的 " **密码** " 选项卡上，选择 **Microsoft 登录** 并使用 [Microsoft 帐户](https://account.microsoft.com/account)登录。 此功能目前仅支持 Microsoft 帐户，但尚不支持工作或学校帐户。

**问**：如何实现在我的手机上使身份验证器成为默认的自动填充提供程序吗？

**答**：请执行以下步骤：

1. 打开验证器 **设置**，在 **Beta** 开启自动 **填充**。
1. 在应用内的 " **密码** " 选项卡上，使用 [Microsoft 帐户](https://account.microsoft.com/account)登录。
1. 执行下列操作之一：

   - 在 iOS 的 " **设置**" 下，选择如何在自动填充设置部分中 **打开自动填充** ，以了解如何将身份验证器设置为默认自动填充提供程序。
   - 在 Android 上的 " **设置**" 下，选择 "自动填充设置" 部分中的 " **设置为自动填充提供程序** "，以将身份验证器设置为默认

**问**：如果 "设置" 中的 "自动 **填充** " 开关灰显？

**答**：自动填充当前为 beta 版本，尚未对所有组织或帐户类型启用。 如果 "**设置**" 中的自动 **填充** 开关为你显示为灰色，则很可能是因为你在使用工作帐户的验证器应用。 你可以在未添加工作帐户的设备上使用此功能。 如果你的组织与 Microsoft 合作，则即使向身份验证器添加工作帐户，也会启用自动 **填充** 开关。

**问**：如何实现停止同步密码吗？

**答**：若要停止同步验证器应用中的密码，请打开 "**设置** 自动  >  **填充设置**" "  >  **同步帐户**"。 在下一个屏幕上，可以选择 " **停止同步并删除所有自动填充数据**"。 这将删除设备中的密码和其他填充数据。 删除自动填充数据不会影响多重身份验证。

**问**：验证器应用如何保护我的密码？

**答**：验证器应用已为多重身份验证和帐户管理提供高级别的安全性，同时还扩展了同一个高安全级别，以管理你的密码。

- **验证器应用需要强身份验证**：登录到身份验证器需要第二个因素。 这意味着即使某人有 Microsoft 帐户的密码，也无法访问验证器应用中的密码。
- 自动 **填充数据使用生物识别和密码进行保护**：在应用或站点上自动填充密码之前，验证器需要生物识别或设备密码。 这可确保即使其他人有权访问你的设备，他们也无法填写或查看你的密码，因为他们无法提供生物识别或设备 PIN。 而且，除非用户提供生物识别或 PIN，否则用户将无法打开密码页面，即使它们关闭应用程序设置中的应用锁定。
- **设备上的加密密码**：设备上的密码已加密，加密/解密密钥永远不会存储在一起并始终动态生成。 仅当用户需要时才会对密码进行解密，也可以在用户需要查看密码时进行解密，这两者都需要生物识别或 PIN。
- **云和网络安全**：云上的密码仅在其到达设备时进行加密和解密。 密码通过受 SSL 保护的 HTTPS 连接进行同步，这可以确保在同步敏感数据时不会对其进行窃听。 我们还确保使用加密哈希函数检查通过网络同步的数据的使用情况， (具体而言，基于哈希的消息身份验证代码) 。

## <a name="autofill-for-it-admins"></a>IT 管理员的自动填充

**问**：我的员工或学生是否会在验证器应用中使用密码自动填充？

**答**：否。 自动填充功能目前处于 beta 阶段，尚未对所有组织或帐户类型启用。 如果你的员工或学生将其工作或学校帐户添加到 Microsoft Authenticator 应用中，则无法对其进行密码自动填充。 当你的员工或学生将其工作或学校帐户作为 [外部或第三方帐户](user-help-auth-app-add-non-ms-account.md)添加到基于云的多因素身份验证时，这一限制例外。

**问**：我是否可以将自动填充功能提供给我的员工 (或学生) ？

**答**：是的。 若要启用你的员工或学生，你的企业或学校可以添加到允许列表。 联系你的支持人员或 Microsoft 联系信息，以将其添加到允许列表。 此外，如果你是组织的 IT 管理员，则还可以填写表单以表达你对在验证器中自动填充的 [允许列表企业](https://aka.ms/RequestAutofillInAuthenticator)中加入的兴趣。

**问**：我的用户的工作或学校帐户密码是否自动同步？

**答**：否。 密码自动填充不会同步用户的工作或学校帐户密码。 当用户访问站点或应用程序时，验证器将提供保存该站点或应用程序的密码，并且仅在用户选择时保存密码。
  
**问**：我是否能仅列出组织中的某些用户自动填充？

**答**：否。 企业目前只能为所有或不允许密码自动填充。 我们将逐步展开这些控件。

**问**：如果我的员工或学生有多个工作或学校帐户怎么办？ 例如，我的员工在其 Microsoft Authenticator 中有多个企业或学校的帐户。

**答**：在验证器应用中添加的所有企业或学校都需要在验证器中列出，以便应用所有者能够使用它。 当你的员工或学生将其工作或学校帐户作为 [外部或第三方帐户](user-help-auth-app-add-non-ms-account.md)添加到基于云的多因素身份验证时，这一限制例外。

## <a name="next-steps"></a>后续步骤

- 如果在获取个人 Microsoft 帐户的验证码时遇到问题，请参阅 [Microsoft 帐户安全信息和验证码](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的“排查验证码问题”部分。

- 如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- 如需有关安全信息的详细信息，请参阅[安全信息（预览版）概述](./security-info-setup-signin.md)

- 如果此文档没有回答问题，请告知我们。 转到 [Microsoft 验证器应用论坛](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) 发布问题并从社区获取帮助或者在此页面上留言，我们会尽快解答相关问题。
