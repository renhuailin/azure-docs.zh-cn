---
title: 将密码导入 Microsoft Authenticator 应用 - Azure AD
description: 如何从常用的密码管理器将密码导入 Microsoft Authenticator 应用。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806149"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>将密码导入 Microsoft Authenticator 应用

Microsoft Authenticator 支持从 Google Chrome、Firefox、LastPass、Bitwarden 和 Roboform 导入密码。 如果 Microsoft 当前不支持现有的密码管理器，可以[将登录凭据手动输入模板 CSV](https://go.microsoft.com/fwlink/?linkid=2134938)。 如果要将现有密码导入 Authenticator 应用并在其中管理这些密码，只需将密码从现有密码管理器导出为逗号分隔的值 (CSV) 格式。 然后，在 Chrome 浏览器扩展中将导出的 CSV 导入到 Authenticator 或直接导入 Authenticator 应用（Android 和 iOS）。

## <a name="import-from-google-chrome-or-android-smart-lock"></a>从 Google Chrome 或 Android Smart Lock 导入

可以在智能手机或桌面计算机上将密码从 Google Chrome 或 Android Smart Lock 导入到 Authenticator。 方法：

- [从 Android 和 iOS 上的 Chrome 导入](#import-from-chrome-on-android-and-ios)
- [从 Chrome 桌面浏览器导入](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>从 Android 和 iOS 上的 Chrome 导入

Android 和 Apple 手机上的 Google Chrome 用户只需简单几步即可直接从他们的手机导入密码。

1. 在手机上安装 Authenticator 应用，并打开“密码”选项卡。

1. 在手机上登录到 Google Chrome。

1. 点击右上角（Android 手机）或右下角（iOS 设备）的 ![Google Chrome 省略号菜单](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png)，然后点击“设置”。

   平台 | 链接
   ---------- | --------
   Android | ![Google Chrome 设置菜单位置](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Google Chrome 设置菜单图标](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. 在“设置”中，打开“密码” 。

   平台 | 链接
   ---------- | --------
   Android | ![Andoid Chrome 密码命令位置](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Apple Chrome 密码命令位置](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. 在 Android 设备上，点击右上角（Android 手机），或点击右下角（iOS 设备）的 ![Google Chrome 省略号菜单](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png)，然后点击“导出密码”。

   平台 | 链接
   ---------- | --------
   Android | ![Android Chrome 导出密码位置](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple Chrome 导出密码位置](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   必须提供 PIN、指纹或面部识别。 确认你的身份，然后再次点击“导出密码”，以开始导出。

1. 在导出密码后，Chrome 会提示选择要导入到哪个应用。 选择“Authenticator”以开始导入密码。在完成后，会收到有关导入状态的通知。

   平台 | 链接
   ---------- | --------
   Android | ![Android Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Apple Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>从 Chrome 桌面浏览器导入

在开始之前，必须安装并登录到 Chrome 浏览器上的 [Microsoft 自动填充扩展](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion)。

1. 在任意浏览器中打开 [Google 密码管理器](https://passwords.google.com)。 如果尚未登录到 Google 帐户，请登录。

1. 选择齿轮图标 ![桌面密码管理器齿轮图标](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) 以打开“密码设置”页。

1. 选择“导出”，然后在下一页上再次选择“导出”，以开始导出密码 。 系统提示确认身份时，提供 Google 密码。 在完成后，会收到有关导入状态的通知。

   ![桌面 Chrome 浏览器导出密码命令位置](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. 打开自动填充 Chrome 扩展，然后选择“设置”。

   ![桌面 Chrome 浏览器自动填充扩展设置位置](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. 选择“导入数据”以打开对话框。 然后，选择“选择文件”，以找到 CSV 文件并将其导入。

   ![桌面 Chrome 浏览器导入数据 CSV 位置](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>从 Firefox 导入

Firefox 只允许从桌面浏览器导出密码，因此，在从 Firefox 导入密码之前，请确保有权访问 Firefox 桌面浏览器。

1. 在桌面上登录到最新版本的 Firefox，并 ![从屏幕右上角选择 Firefox“汉堡”](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 菜单。

1. 设置“登录名和密码”。

   ![桌面 Firefox 浏览器登录名和密码位置](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. 从 Firefox Lockwise 页中，选择 ![Firefox 省略号菜单](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png)菜单，选择“导出登录名”，然后通过选择“导出”来确认意向 。 系统会提示通过输入 PIN、设备密码或通过扫描指纹来确认身份。 在成功识别后，Firefox 会将 CSV 格式的密码导出到所选位置。

   ![桌面 Firefox 浏览器导出密码位置](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. 可以从桌面浏览器或 iOS 或 Android 手机将密码导入到 Authenticator。 如果要导入到手机上的 Authenticator 应用，请执行以下操作：

      1. 使用首选和安全的方式在 Android 或 iOS 手机上传输导出的 CSV 文件，然后下载该文件。 接下来，与 Authenticator 应用共享该 CSV 文件以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 在将密码成功导入到 Authenticator 后，从桌面或移动电话中删除该 CSV 文件。

## <a name="import-from-lastpass"></a>从 LastPass 导入

LastPass 只支持从桌面浏览器导出密码，因此，在开始导入密码之前，请确保有权访问桌面浏览器。

1. 登录到 [LastPass 网站](https://lastpass.com)并选择“高级选项”，然后选择“导出” 。

   ![桌面 LastPass 导出密码位置](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. 系统提示时，提供主密码来确认自己的身份。 之后，将会在该网页上看到导出的密码。

1. 复制该网页的内容。

1. 打开记事本（或常用的文本编辑器）并粘贴复制的内容。

1. 通过选择“文件”&gt;“另存为”保存此记事本文件 。 在桌面上的安全位置提供以“.csv”结尾的名称（例如 LastPass.csv）。

   ![桌面 LastPass 保存 CSV 文件](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. 可以在桌面浏览器中或 iOS 或 Android 手机上将密码导入到 Authenticator。 如果要导入到手机上的 Authenticator 应用，请执行以下操作：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后与 Authenticator 应用共享该 CSV 文件，以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android LastPass 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple LastPass 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 在将密码成功导入到 Authenticator 后，从桌面或移动电话中删除该 CSV 文件。

## <a name="import-from-bitwarden"></a>从 Bitwarden 导入

Bitwarden 只支持从桌面浏览器导出密码，因此，在开始导入密码之前，请确保有权访问桌面浏览器。

1. 登录 https://vault.bitwarden.com/ 并选择“工具”&gt;“导出保管库” 。 选择 CSV 作为文件格式，提供主密码，然后选择“导出保管库”以开始导出。

   ![Bitwarden 导出保管库位置](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. 可以在桌面浏览器中或 iOS 或 Android 手机上将密码导入到 Authenticator。 如果要导入到手机上的 Authenticator 应用，请执行以下操作：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后与 Authenticator 应用共享该 CSV 文件，以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android Bitwarden 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Bitwarden 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 在将密码成功导入到 Authenticator 后，从桌面或移动电话中删除该 CSV 文件。

## <a name="import-from-roboform"></a>从 Roboform 导入

Roboform 只允许从其桌面应用导出密码，因此，在开始导入之前，请确保在桌面上有权访问 Roboform 应用。

1. 从桌面客户端启动 RoboForm，并登录到你的帐户。

1. 从“Roboform”菜单中选择“选项” 。

   ![桌面 Roboform 选项菜单](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. 选择“帐户和数据”&gt;“导出” 。

   ![桌面 Roboform 导出命令位置](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. 选择安全的位置来保存导出的文件。 选择“登录名”作为“数据”类型，并选择 CSV 文件作为格式，然后选择“导出”  。

   ![桌面 Roboform 导出对话框](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. 确认你的意图，然后将该 CSV 文件导出到所选位置。

   ![桌面 Roboform 导出确认对话框](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. 可以在桌面浏览器中或 iOS 或 Android 手机上将密码导入到 Authenticator。 如果要导入到手机上的 Authenticator 应用，请执行以下操作：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后与 Authenticator 应用共享该 CSV 文件，以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android Roboform 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Roboform 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 在将密码成功导入到 Authenticator 后，从桌面或移动电话中删除该 CSV 文件。

## <a name="import-by-creating-a-csv"></a>通过创建 CSV 进行导入

如果本文未列出从密码管理器导入密码的步骤，可以创建一个 CSV，以用于将密码导入到 Authenticator。 Microsoft 建议在桌面上执行以下步骤，以易于设置格式。

1. 在桌面上，[下载并打开导入模板](https://go.microsoft.com/fwlink/?linkid=2134938)。 如果你是 Apple iPhone、Safari 和 Keychain 用户，则现在可跳至步骤 4。

1. 从现有的密码管理器将密码导出到非加密的 CSV 文件中。

1. 从导出的 CSV 中将相关列复制到模板 CSV，然后保存。

1. 如果没有导出的 CSV，则可以将每个登录名从现有密码管理器复制到模板 CSV。 请勿删除或更改标题行。 在完成后，请先验证数据的完整性，然后再开始下一步。

1. 可以在桌面浏览器中或 iOS 或 Android 手机上将密码导入到 Authenticator。 如果要导入到手机上的 Authenticator 应用，请执行以下操作：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后与 Authenticator 应用共享该 CSV 文件，以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android CSV 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple CSV 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 在将密码成功导入到 Authenticator 后，从桌面或移动电话中删除该 CSV 文件。

## <a name="troubleshooting-steps"></a>疑难解答步骤

导入失败的最常见原因是 CSV 文件中的不正确格式。 可尝试以下步骤来排查问题。

- 请查看本文，以了解我们是否支持从当前的密码管理器导入密码。 如果支持，可能需要按照各提供程序的所述步骤重试导入。

- 如果当前不支持导入密码管理器的格式，则可以通过[手动创建 CSV 文件](#import-by-creating-a-csv)来重试。

- 可以按以下建议来验证 CSV 数据的完整性：

  - 第一行必须包含包具有三列的标头：“url”、“用户名”和“密码”  。

  - 每一行都必须在“url”和“密码”列中包含值 。

- 可以通过将内容粘贴到 [CSV 模板文件](https://go.microsoft.com/fwlink/?linkid=2134938)来重新创建 CSV。

- 如果这些操作都不起作用，请使用 Authenticator 应用设置中的“发送反馈”链接来报告问题。
