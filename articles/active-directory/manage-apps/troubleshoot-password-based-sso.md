---
title: 在 Azure Active Directory 中排查基于密码的单一登录问题
description: 排查配置为基于密码的单一登录的 Azure AD 应用的问题。
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 32b099b72a55e768087f6123e684f0107e1e1e57
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739959"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>在 Azure AD 中排查基于密码的单一登录问题

若要使用“我的应用”中的“基于密码的单一登录 (SSO)” ，必须安装浏览器扩展。 选择某个已配置基于密码的 SSO 的应用时，会自动下载此扩展。 若要了解如何从最终用户的角度使用“我的应用”，请参阅[“我的应用”门户帮助](../user-help/my-apps-portal-end-user-access.md)。

## <a name="my-apps-browser-extension-not-installed"></a>“我的应用”浏览器扩展未安装

请确保已安装浏览器扩展。 要了解更多信息，请参见[计划 Azure Active Directory“我的应用”部署](my-apps-deployment-plan.md)。

## <a name="single-sign-on-not-configured"></a>未配置单一登录

请确保已配置基于密码的单一登录。 若要了解详细信息，请参阅[配置基于密码的单一登录](configure-password-single-sign-on-non-gallery-applications.md)。

## <a name="users-not-assigned"></a>未分配用户

请确保已将用户分配到应用。 若要了解详细信息，请参阅[将用户或组分配到应用](assign-user-or-group-access-portal.md)。

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>凭据已填写，但扩展却未提交凭据

这种问题通常发生在以下情景中：应用程序供应商为了添加字段而在近期更改了他们的登录页面、改变了用于检测用户名和密码字段的标识符，或修改了登录体验作用于应用程序的方式。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题需要一些时间才能解决。 如果其中一个集成无法正确工作，请打开支持案例以尽快解决这一问题。

如果与该应用程序的供应商联系，请向他们发送我们的方法，这样，Microsoft 就可以与他们合作，将其应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](../develop/v2-howto-app-gallery-listing.md)来让他们开始工作。

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>凭据已填写并提交，但页面却显示凭据不正确

若要解决此问题，请先尝试以下操作：

- 先让用户使用为他们保存的凭据尝试 **直接登录应用程序网站**。

  - 如果登录有效，再让用户在[我的应用](https://myapps.microsoft.com/)“应用”部分中的“应用程序磁贴”上，单击“更新凭据”，以将凭据更新为已知的最新有效的用户名和密码。

  - 如果或另一个管理员为该用户分配凭据，请导航到该应用程序的“用户和组”选项卡、选择分配并单击“更新凭据”按钮，找到该用户或组的应用程序分配。

- 如果用户自己分配凭据，让用户“检查以确保他们的密码在应用程序中未过期”，如果已经过期，直接登录应用程序 **更新过期密码**。

  - 密码在应用程序中更新好后，要求用户在[我的应用](https://myapps.microsoft.com/)“应用”部分中的“应用程序磁贴”上，单击“更新凭据”按钮，以将凭据更新到已知的最新有效的用户名和密码。

  - 如果或另一个管理员为该用户分配凭据，请导航到该应用程序的“用户和组”选项卡、选择分配并单击“更新凭据”按钮，找到该用户或组的应用程序分配。

- 确保“我的应用”浏览器扩展正在运行并且已在用户浏览器中启用。

- 确保用户未在“incognito”、“inPrivate”或“专用”模式下试图从“我的应用”登录应用程序。 在这些模式下“我的应用”扩展不受支持。

如果前面的建议无效，可能是应用程序端已发生变更，已暂时中断了应用程序与 Azure AD 的集成。 例如，当应用程序供应商在页面上引入与手动和自动化输入表现不同的脚本，从而导致自动集成（就像我们这样的）中断时，就会发生这样情况。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在应用程序集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题可能需要一些时间才能解决。 当集成无法正常工作时，可以打开支持案例以尽快解决这一问题。

除此之外，**如果与该应用程序的供应商联系，请** **向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](../develop/v2-howto-app-gallery-listing.md)来让他们开始工作。

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>检查应用程序的登录页面近期是否已变更，或需要其他字段

如果应用程序的登录页面已彻底变更，有时这会导致我们的集成中断。 例如，当应用程序供应商向体验添加登录字段、验证码或多重身份验证时就会出现这种情况。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在应用程序集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题可能需要一些时间才能解决。 当集成无法正常工作时，可以打开支持案例以尽快解决这一问题。

除此之外，**如果与该应用程序的供应商联系，请** **向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](../develop/v2-howto-app-gallery-listing.md)来让他们开始工作。

## <a name="capture-sign-in-fields-for-an-app"></a>捕获应用的登录字段

仅支持 HTML 的登录页面支持登录字段捕获。 非标准登录页面不支持登录字段捕获，例如使用 Adobe Flash 或其他非 HTML 支持的技术的页面。

有两种方法可以捕获自定义应用的登录字段：

- 如果登录页面对用户名和密码字段使用已知 DIV ID，则“自动登录字段捕获”很适合大多数支持 HTML 的登录页面。 抓取页面上的 HTML 用于查找与特定条件匹配的 DIV ID。 保存元数据，以便以后可将其重播到应用中。

- 如果应用供应商未标记登录输入字段，则使用“手动登录字段捕获”。 如果供应商呈现多个无法自动检测到的字段，则也可以使用手动捕获。 Azure Active Directory (Azure AD) 可以存储登录页面上的所有字段的数据，只要知道这些字段在页面上的位置即可。

一般情况下，如果自动登录字段捕获不起作用，请尝试手动选项。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自动捕获应用的登录字段

若要使用自动登录字段捕获来配置基于密码的 SSO，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)。 以“全局管理员”或“共同管理员”身份登录。
2. 在左侧的导航窗格中，选择“所有服务”打开 Azure AD 扩展。
3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”。
4. 在 Azure AD 导航窗格中选择“企业应用程序”。
5. 选择“所有应用程序”，查看应用程序列表。
   > [!NOTE]
   > 如果看不到所需的应用，请使用“所有应用程序”列表顶部的“筛选器”控件。 将“显示”选项设置为“所有应用程序”。
6. 选择要为 SSO 配置的应用。
7. 在应用加载后，在左侧的导航窗格中选择“单一登录”。
8. 选择“基于密码的登录”模式。
9. 输入“登录 URL”，这是用户登录应用时在其中输入用户名和密码的页面的 URL。 确保登录字段在提供的 URL 的页面上可见。
10. 选择“保存”。
    会自动为用户名和密码输入框抓取该页面。 现在可以使用 Azure AD 通过“我的应用”浏览器扩展将密码安全地传输到该应用。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手动捕获应用的登录字段

若要手动捕获登录字段，必须安装“我的应用”浏览器扩展。 此外，浏览器不能在“inPrivate”、“incognito”或“专用”模式下运行。

若要使用手动登录字段捕获来配置应用基于密码的 SSO，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)。 以“全局管理员”或“共同管理员”身份登录。
2. 在左侧的导航窗格中，选择“所有服务”打开 Azure AD 扩展。
3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”。
4. 在 Azure AD 导航窗格中选择“企业应用程序”。
5. 选择“所有应用程序”，查看应用程序列表。
   > [!NOTE]
   > 如果看不到所需的应用，请使用“所有应用程序”列表顶部的“筛选器”控件。 将“显示”选项设置为“所有应用程序”。
6. 选择要为 SSO 配置的应用。
7. 在应用加载后，在左侧的导航窗格中选择“单一登录”。
8. 选择“基于密码的登录”模式。
9. 输入“登录 URL”，这是用户登录应用时在其中输入用户名和密码的页面。 确保登录字段在提供的 URL 的页面上可见。
10. 选择“配置 ***应用名称&lt;&gt;密码单一登录设置”***。
11. 选择“手动检测登录字段”。
12. 选择“确定”  。
13. 选择“保存”。 
14. 按照说明使用“我的应用”。

## <a name="troubleshoot-problems"></a>排查问题

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到“无法找到 URL 的任一登录字段”错误

自动检测登录字段失败时会收到此错误消息。 若要解决此问题，请尝试手动登录字段检测。 请参阅本文的[手动捕获应用程序的登录字段](#manually-capture-sign-in-fields-for-an-app)部分。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到“无法保存单一登录配置”错误

更新 SSO 配置很少会失败。 要解决这一问题，请尝试再次保存配置。

如果仍然遇到此错误，请打开支持案例。 包括本文的[查看门户通知详细信息](#view-portal-notification-details)和[向支持工程师发送通知详细信息以获取帮助](#send-notification-details-to-a-support-engineer-to-get-help)部分中所述的信息。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我无法手动检测应用的登录字段

手动检测不起作用时，可能会观察到以下行为：

- 手动捕获进程看似有效，但捕获的字段不正确。
- 当捕获进程运行时，正确的字段不突出显示。
- 捕获进程如期将你带到应用的登录页面，但无任何反应。
- 手动捕获看似有效，但当用户从“我的应用”导航到应用时，未发生 SSO。

如果遇到以上任何问题，请执行以下操作：

- 确保已安装并启用“我的应用”浏览器扩展的最新版本。
- 在捕获过程中，确保你的浏览器不处于“incognito”、“inPrivate”或“专用”模式。 在这些模式下“我的应用”扩展不受支持。
- 确保用户未在 *incognito*、*inPrivate* 或 *专用* 模式下试图从“我的应用”登录应用。
- 再次尝试手动捕获进程。 确保红色标记在正确的字段上。
- 如果手动捕获进程看似已停止响应或登录页面没有响应，请再次尝试手动捕获进程。 但是，这次在进程完成后，请按 F12 键打开浏览器的开发人员控制台。 选择“控制台”选项卡。键入“window.location="配置应用时指定的登录 URL"***&lt;&gt;***”，然后按 Enter。 这会重定向至结束捕获进程并存储已捕获的字段的页面。

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>我无法将其他用户添加到基于密码的 SSO 应用

基于密码的 SSO 应用的用户数限制为 48 个。 因此，每个应用的用户名/密码对的密钥限制为 48 个。
如果要添加其他用户，可以：

- 添加应用的其他实例
- 先删除不再使用应用的用户

## <a name="request-support"></a>请求支持

如果在设置 SSO 并分配用户时收到错误消息，请打开支持票证。 请尽可能多地包含以下信息：

- 相关错误 ID
- UPN（用户电子邮件地址）
- TenantID
- 浏览器类型
- 发生错误时的时区和时间/期限
- Fiddler 跟踪

### <a name="view-portal-notification-details"></a>查看门户通知的详细信息

若要查看任何门户通知的详细信息，请执行以下步骤：

1. 选择 Azure 门户右上角的“通知”图标（铃铛）。
2. 选择显示“错误”状态的任何通知。 （它们标有红色的“!”。）
   > [!NOTE]
   > 你无法选择处于“成功”或“正在进行”状态的通知。
3. “通知详细信息”窗格随即打开。 阅读此信息以了解此问题。
4. 如果仍需要帮助，可以与支持工程师或产品组共享此信息。 选择“复制错误”文本框右侧的“复制”图标，复制要共享的通知详细信息。

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>向支持工程师发送通知详细信息以获取帮助

请务必共享此部分中列出的所有详细信息并提供支持，以便他们能够快速帮助你。 要记录它，可以进行屏幕截图或选择“复制错误”。

以下信息说明每个通知项的含义，并提供示例。

#### <a name="essential-notification-items"></a>基本通知项

- **标题**：通知的描述性标题。

   示例：应用程序代理设置

- **说明**：由操作导致的所发生情况的说明。

   示例：输入的内部 URL 已被其他应用程序使用。

- **通知 ID**：通知的唯一 ID。

    示例：clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068

- **客户端请求 ID**：由浏览器发出的特定请求 ID。

    示例：302fd775-3329-4670-a9f3-bea37004f0bc

- **时间戳 UTC**：在通知发生期间的时间戳 (UTC)。

    示例：2017-03-23T19:50:43.7583681Z

- **内部事务 ID**：可用于在系统中查找错误的内部 ID。

    示例：71a2f329-ca29-402f-aa72-bc00a7aca603

- **UPN**：执行操作的用户。

    示例：tperkins\@f128.info

- **租户 ID**：执行操作的用户所属的租户的唯一 ID。

    示例：7918d4b5-0442-4a97-be2d-36f9f9962ece

- **用户对象 ID**：执行操作的用户的唯一 ID。

    示例：17f84be4-51f8-483a-b533-383791227a99

#### <a name="detailed-notification-items"></a>详细通知项

- **显示名称**：（可以为空）错误的更详细的显示名称。

    示例：应用程序代理设置

- **状态**：通知的具体状态。

    示例：失败

- **对象 ID**：（可以为空）对其执行了操作的对象 ID。

   示例：8e08161d-f2fd-40ad-a34a-a9632d6bb599

- **详细信息**：由操作导致的所发生情况的详细说明。

    示例：内部 url '<https://bing.com/>' 无效，因为它已在使用中。

- **复制错误**：选择“复制错误”文本框右侧的“复制”图标，可复制通知详细信息，以获取支持帮助。

    示例：```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>后续步骤

- [应用程序管理的快速入门系列](view-applications-portal.md)
- [计划“我的应用”部署](my-apps-deployment-plan.md)
