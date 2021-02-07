---
title: 将密码导入 Microsoft Authenticator 应用-Azure AD
description: 如何从常用密码管理器将密码导入 Microsoft 身份验证应用。
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
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806149"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>将密码导入 Microsoft Authenticator 应用

Microsoft Authenticator 支持从 Google Chrome、Firefox、LastPass、Bitwarden 和 Roboform 导入密码。 如果 Microsoft 当前不支持你的现有密码管理器，你可以在 [模板 CSV 中手动输入登录凭据](https://go.microsoft.com/fwlink/?linkid=2134938)。 若要导入现有密码并在验证器应用程序中对其进行管理，只需将密码从现有密码管理器导出到以逗号分隔的值 (CSV) 格式。 然后，将导出的 CSV 导入到 Chrome 浏览器扩展中的身份验证器或直接 (Android 和 iOS) 中的验证器应用。

## <a name="import-from-google-chrome-or-android-smart-lock"></a>从 Google Chrome 或 Android Smart Lock 导入

可以将密码从 Google Chrome 或 Android Smart Lock 导入到 smartphone 或台式计算机上的身份验证器。 方法：

- [从 Android 和 iOS 上的 Chrome 导入](#import-from-chrome-on-android-and-ios)
- [从 Chrome 桌面浏览器导入](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>从 Android 和 iOS 上的 Chrome 导入

Android 和 Apple 手机上的 Google Chrome 用户可以直接从手机导入密码，只需几个简单的步骤。

1. 在手机上安装验证器应用，并打开 " **密码** " 选项卡。

1. 登录到手机上的 Google Chrome。

1. ![对于 Android 手机，请点击右上角的 Google Chrome 省略号菜单 ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) ，然后点击 "**设置"。**

   平台 | 链接
   ---------- | --------
   Android | ![Google Chrome 设置菜单位置](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Google Chrome 设置菜单图标](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. 在 " **设置**" 中，打开 **密码**。

   平台 | 链接
   ---------- | --------
   Android | ![Andoid Chrome 密码命令位置](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Apple Chrome 密码命令位置](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. 在 Android 设备上，点击右上角的 " ![ android 手机" 或 "iOS 设备" 右下方的 "Google Chrome 省略号" 菜单 ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) ，然后点击 " **导出密码**"。

   平台 | 链接
   ---------- | --------
   Android | ![Android Chrome 导出密码位置](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple Chrome 导出密码位置](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   必须提供 PIN、指纹或面部识别。 确认你的身份，然后点击 " **导出密码** " 以开始导出。

1. 导出密码后，Chrome 会提示你选择要导入的应用。 选择 " **身份验证** 器" 以开始导入密码。完成后，会通知你导入状态。

   平台 | 链接
   ---------- | --------
   Android | ![Android Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Apple Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>从 Chrome 桌面浏览器导入

在开始之前，必须安装并登录到 Chrome 浏览器上的 [Microsoft 自动填充扩展](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) 。

1. 在任意浏览器中打开 [Google 密码管理器](https://passwords.google.com) 。 如果你尚未登录，请登录到你的 Google 帐户。

1. 选择齿轮图标 ![桌面密码管理器齿轮图标](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) 打开 "密码设置" 页。

1. 选择 " **导出**"，然后在下一页上选择 " **导出** " 以开始导出密码。 系统提示确认身份时，提供 Google 密码。 完成后，会通知你导入状态。

   ![桌面 Chrome 浏览器导出密码命令位置](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. 打开自动填充 Chrome 扩展，然后选择 " **设置**"。

   ![桌面 Chrome 浏览器自动填充扩展设置位置](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. 选择 " **导入数据** " 以打开对话框。 然后，选择 " **选择文件** " 以查找并导入 CSV 文件。

   ![桌面 Chrome 浏览器导入数据 CSV 位置](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>从 Firefox 导入

Firefox 仅允许从桌面浏览器导出密码，因此在从 Firefox 导入密码之前，请确保你有权访问 Firefox 桌面浏览器。

1. 在桌面上登录到最新版本的 Firefox，并选择 ![Firefox "汉堡" 菜单](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 位于屏幕右上方的菜单。

1. 选择 " **登录名和密码**"。

   ![桌面 Firefox 浏览器登录名和密码位置](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. 从 "Firefox Lockwise" 页中，选择 " ![ firefox 省略号菜单" ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 菜单，选择 " **导出登录名**"，然后通过选择 " **导出**" 确认意向。 系统会提示你通过输入 PIN、设备密码或扫描你的指纹来标识自己。 成功识别后，Firefox 会将 CSV 格式的密码导出到所选位置。

   ![桌面 Firefox 浏览器导出密码位置](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. 可以从桌面浏览器或 iOS 或 Android 手机将密码导入到身份验证器。 若要导入到手机上的验证器应用：

      1. 使用首选和安全的方式在 Android 或 iOS 手机上传输导出的 CSV 文件，然后下载该文件。 接下来，与验证器应用共享 CSV 文件以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Chrome 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 将密码成功导入到身份验证器后，请从桌面或移动电话中删除该 CSV 文件。

## <a name="import-from-lastpass"></a>从 LastPass 导入

LastPass 仅支持从桌面浏览器导出密码，因此在开始导入密码之前，请确保你有权访问桌面浏览器。

1. 登录到 [LastPass](https://lastpass.com) 网站并选择 " **高级选项**"，然后选择 " **导出**"。

   ![桌面 LastPass 导出密码位置](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. 提供主密码时，通过提供提示来确定自己的身份。 之后，你将在网页上看到导出的密码。

1. 复制网页的内容。

1. 打开记事本 (或你喜欢的文本编辑器) 并粘贴复制的内容。

1. 通过选择 " **文件** &gt; **另存为**" 保存此记事本文件。 提供以 ".csv" 结尾的名称， (例如 LastPass.csv) 在桌面上的安全位置。

   ![桌面 LastPass 保存 CSV 文件](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. 可以在桌面浏览器中或在 iOS 或 Android 手机上将密码导入到身份验证器。 若要导入到手机上的验证器应用：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后，与验证器应用共享 CSV 文件以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android LastPass 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple LastPass 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 将密码成功导入到身份验证器后，请从桌面或移动电话中删除该 CSV 文件。

## <a name="import-from-bitwarden"></a>从 Bitwarden 导入

Bitwarden 仅支持从桌面浏览器导出密码，因此在开始导入密码之前，请确保你有权访问桌面浏览器。

1. 登录 https://vault.bitwarden.com/ 并选择 " **工具**" " &gt; **导出保管库**"。 选择 "CSV" 格式的文件格式，提供主密码，然后选择 " **导出保管库** " 以开始导出。

   ![Bitwarden 导出保管库位置](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. 可以在桌面浏览器中或在 iOS 或 Android 手机上将密码导入到身份验证器。 若要导入到手机上的验证器应用：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后，与验证器应用共享 CSV 文件以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android Bitwarden 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Bitwarden 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 将密码成功导入到身份验证器后，请从桌面或移动电话中删除该 CSV 文件。

## <a name="import-from-roboform"></a>从 Roboform 导入

Roboform 仅允许从其桌面应用导出密码，因此在开始导入之前，请确保你有权访问桌面上的 Roboform 应用。

1. 从你的桌面客户端启动 RoboForm，并登录到你的帐户。

1. 从 " **Roboform** " 菜单中选择 "**选项**"。

   ![桌面 Roboform 选项菜单](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. 选择 " **帐户 & 数据** &gt; **导出**"。

   ![Desktop Roboform 导出命令位置](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. 选择一个安全的位置以保存导出的文件。 选择 " **登录** " 作为 " **数据** 类型"，并选择 "CSV 文件" 作为 "格式"，然后选择 " **导出**"。

   !["桌面 Roboform 导出" 对话框](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. 确认你的意图，然后将 CSV 文件导出到所选位置。

   !["桌面 Roboform 导出确认" 对话框](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. 可以在桌面浏览器中或在 iOS 或 Android 手机上将密码导入到身份验证器。 若要导入到手机上的验证器应用：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后，与验证器应用共享 CSV 文件以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android Roboform 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Roboform 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 将密码成功导入到身份验证器后，请从桌面或移动电话中删除该 CSV 文件。

## <a name="import-by-creating-a-csv"></a>通过创建 CSV 进行导入

如果本文未列出从密码管理器导入密码的步骤，可以创建一个 CSV，以便将密码导入到身份验证器。 Microsoft 建议您在桌面上执行以下步骤，以便进行格式设置。

1. 在桌面上， [下载并打开导入模板](https://go.microsoft.com/fwlink/?linkid=2134938)。 如果你是 Apple iPhone、Safari 和密钥链用户，则现在可以跳到步骤4。

1. 从非加密 CSV 文件中的现有密码管理器导出密码。

1. 将导出的 CSV 中的相关列复制到模板 CSV，并保存。

1. 如果你没有导出的 CSV，则可以将每个登录名从现有密码管理器复制到模板 CSV。 不要删除或更改标题行。 完成后，请在开始下一步之前验证数据的完整性。

1. 可以在桌面浏览器中或在 iOS 或 Android 手机上将密码导入到身份验证器。 若要导入到手机上的验证器应用：

      1. 使用首选和安全的方式在智能手机上传输导出的 CSV 文件，然后下载该文件。 然后，与验证器应用共享 CSV 文件以开始导入。

         平台 | 链接
         ---------- | --------
         Android | ![Android CSV 导入密码位置](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple CSV 导入密码位置](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 将密码成功导入到身份验证器后，请从桌面或移动电话中删除该 CSV 文件。

## <a name="troubleshooting-steps"></a>疑难解答步骤

导入失败的最常见原因是 CSV 文件中的格式不正确。 你可以尝试以下步骤来解决此问题。

- 请查看本文，查看是否已支持从当前密码管理器导入密码。 如果是这样，您可能需要按照各自提供程序所述的步骤重试导入。

- 如果当前不支持导入密码管理器的格式，则可以通过 [手动创建 CSV 文件](#import-by-creating-a-csv)来重试。

- 可以通过以下建议验证 CSV 数据的完整性：

  - 第一行必须包含包含三列的标头： **url**、 **用户名** 和 **密码**。

  - 每一行都必须在 " **url** " 和 " **密码** " 列中包含一个值。

- 可以通过将内容粘贴到 [csv 模板文件](https://go.microsoft.com/fwlink/?linkid=2134938)来重新创建 csv。

- 如果其他操作都不起作用，请使用来自验证器应用设置的 " **发送反馈** " 链接报告你的问题。
