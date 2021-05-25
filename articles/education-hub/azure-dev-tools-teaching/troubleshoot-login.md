---
title: 解决 Azure Dev Tools for Teaching 的登录错误
description: 本文介绍学生登录 Azure Dev Tools for Teaching 后收到错误消息时应采取的操作。
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87095878"
---
# <a name="troubleshooting-student-login-issues"></a>学生登录问题故障排除
若要访问 Azure Dev Tools for Teaching，用户需要具有 Microsoft 帐户 (MSA)。 如果学生的帐户还不是 MSA 或未链接到 MSA，将自动指导学生创建一个 MSA。 如果域与 Active Directory 关联，则该域下的所有帐户都已被视为 MSA。

如果学生尝试登录并收到以下错误消息，请使用下面所述的其中一种解决方案。

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="登录错误消息。" border="false":::

有两种解决方案：创建新的 Microsoft 帐户或使用现有的 Microsoft 帐户。

## <a name="create-a-new-microsoft-account"></a>创建新的 Microsoft 帐户
### <a name="use-a-university-email-address"></a>使用大学电子邮件地址
如果使用大学电子邮件地址登录（例如，`John.Smith@university.edu`），则需要使用该电子邮件地址创建一个 Microsoft 帐户。 创建帐户是免费的，只需几分钟即可完成。 如果有 Microsoft 帐户，则可以使用单个用户名和密码自动登录到所有 Microsoft 产品。

### <a name="use-a-personal-email-address"></a>使用个人电子邮件地址
如果使用个人电子邮件地址登录（例如，`John.Smith@email.com`）但也有大学电子邮件地址，请尝试使用大学电子邮件地址。 如果以前使用个人电子邮件地址登录贵机构的网上商店或没有大学电子邮件地址，则需要创建一个 Microsoft 帐户，并将其链接到个人电子邮件地址。

## <a name="use-an-existing-microsoft-account"></a>使用现有的 Microsoft 帐户
如果学生有现有的 Microsoft 帐户（例如 Xbox），则可以将该帐户连接到 Azure Dev Tools 帐户。

1. 转到  https://account.microsoft.com 。
1. 使用 Microsoft 帐户凭据登录。
1. 从顶部功能区菜单中选择“你的信息”。

1. 单击“管理 Microsoft 的登录方式”。 系统会要求验证身份。 你将通过电子邮件收到一个安全代码。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="管理登录。" border="false":::

1. 输入电子邮件发送的安全代码。

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="输入登录代码。" border="false":::！

1. 单击“添加电子邮件”到帐户，然后输入大学电子邮件地址。
下一次登录时，可以使用大学电子邮件地址来访问 Azure Dev Tools for Teaching。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="管理 Microsoft 的登录方式。" border="false":::

## <a name="next-steps"></a>后续步骤
- [常见问题解答](program-faq.md)

- [支持选项](program-support.md)
