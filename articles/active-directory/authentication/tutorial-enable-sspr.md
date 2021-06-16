---
title: 启用 Azure Active Directory 自助式密码重置
description: 本教程介绍如何为一组用户启用 Azure Active Directory 自助式密码重置，以及如何测试密码重置过程。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e000a712e72498c21366b30b0c85e62f957c28c4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110784808"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>教程：使用户能够使用 Azure Active Directory 自助式密码重置来解锁其帐户或重置密码

Azure Active Directory (Azure AD) 自助式密码重置 (SSPR) 使用户能够更改或重置其密码，而不需要管理员或支持人员的干预。 如果 Azure AD 锁定用户帐户或用户忘记了自己的密码，他们可以按照提示自行解锁，恢复工作。 当用户无法登录到其设备或应用程序时，此功能可减少呼叫支持人员的次数，降低生产力损失。 建议观看[如何在 Azure AD 中启用和配置 SSPR](https://www.youtube.com/watch?v=rA8TvhNcCvQ) 这段视频。 我们还为 IT 管理员提供了有关如何[使用 SSPR 解决六个最常见的最终用户错误消息](https://www.youtube.com/watch?v=9RPrNVLzT8I)的视频。

> [!IMPORTANT]
> 本教程向管理员展示如何启用自助式密码重置。 如果你是已注册自助式密码重置的最终用户并且需要恢复你的帐户，请转到 [Microsoft Online 密码重置](https://passwordreset.microsoftonline.com/)。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为一组 Azure AD 用户启用自助式密码重置
> * 设置身份验证方法和注册选项
> * 以用户身份测试 SSPR 过程

## <a name="prerequisites"></a>先决条件

你需有以下资源和特权才能完成本教程：

* 一个至少启用了 Azure AD Free 或试用版许可证的有效 Azure AD 租户。 在免费层中，SSPR 仅适用于 Azure AD 中的云用户。 免费层支持密码更改，不支持密码重置。 
    * 对于本系列的后续教程，系统需要 Azure AD Premium P1 或试用版许可证才能将本地密码写回。
    * 如果需要，[免费创建一个 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个拥有“全局管理员”特权的帐户。
* 你知道其密码的非管理员测试用户，例如 testuser。 在本教程，你将使用此帐户测试最终用户的 SSPR 体验。
    * 如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../fundamentals/add-users-azure-active-directory.md)。
* 该非管理员用户所属的组，例如 SSPR-Test-Group。 在本教程中，你将为此组启用 SSPR。
    * 如需创建一个组，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="enable-self-service-password-reset"></a>启用自助式密码重置

Azure AD 可让你为“无”、“选定”或“所有”用户启用 SSPR。 借助这种粒度，可以选择一部分用户来测试 SSPR 注册过程和工作流。 如果你很熟悉能够与一组更广泛的用户传达相关要求的过程和时机，则可以选择一组用户来为他们启用 SSPR。 或者，可为 Azure AD 租户中的每个人启用 SSPR。

> [!NOTE]
> 目前，你只能使用 Azure 门户为 SSPR 启用一个 Azure AD 组。 Azure AD 作为 SSPR 更广泛部署的一部分，可以支持嵌套组。 

在本教程，为测试组中的一组用户设置 SSPR。 使用 SSPR-Test-Group 并根据需要提供自己的 Azure AD 组：

1. 使用拥有全局管理员权限的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“密码重置”。 
1. 在“属性”页的“已启用自助式密码重置”选项下，选择“选择组”
1. 浏览并选择 Azure AD 组（例如 SSPR-Test-Group），然后选择“选择”。 

    [![在 Azure 门户中选择要为其启用自助式密码重置的组](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. 若要为所选用户启用 SSPR，请选择“保存”。

## <a name="select-authentication-methods-and-registration-options"></a>选择身份验证方法和注册选项

当用户需要解锁帐户或重置其密码时，系统会提示他们选择另一种确认方法。 这一额外的身份验证因素确保 Azure AD 仅完成已批准的 SSPR 事件。 可以根据用户提供的注册信息，选择允许哪些身份验证方法。

1. 从“身份验证方法”页的左侧菜单中，将必填的方法数重置为 1。 

    若要提高安全性，可以增加 SSPR 所需的身份验证方法数。

1. 选择组织允许的“可供用户使用的方法”。 对于本教程，请选中相应的框来启用以下方法：

    * *移动应用通知*
    * *移动应用代码*
    * *电子邮件*
    * *移动电话*

    你可以根据需要启用其他身份验证方法（如办公电话或安全性问题），以满足业务要求。 

1. 若要应用身份验证方法，请选择“保存”。

用户必须先注册其联系信息，然后才能解锁其帐户或重置密码。 Azure AD 使用此联系信息来实现前面步骤中的不同身份验证方法设置。

管理员可以手动提供此联系信息，或者用户可以转到注册门户来自行提供信息。 在本教程，将 Azure AD 设置为在用户下次登录时提示其进行注册。

1. 在“注册”页的左侧菜单中，对“要求用户在登录时注册”选择“是”。
1. 将“在多少天后要求用户重新确认其身份验证信息”设置为“180”。

    务必将联系人信息保持最新状态。 如果在启动 SSPR 事件时联系信息已过时，用户可能无法解锁帐户或重置密码。

1. 若要应用注册设置，请选择“保存”。

## <a name="set-up-notifications-and-customizations"></a>配置通知和自定义项

若要使用户了解帐户活动，你可以设置 Azure AD 在发生 SSPR 事件时发送电子邮件通知。 这些通知可以涵盖普通用户帐户和管理员帐户。 对于管理员帐户，当使用 SSPR 重置特权管理员帐户密码时，此通知将提供另外一层意识。 如果有人对管理员帐户使用 SSPR，Azure AD 会通知所有全局管理员。

1. 在“通知”页的左侧菜单中，设置以下选项：

   * 将“重置密码时通知用户”选项设置为“是”。
   * 将“当其他管理员重置其密码时通知所有管理员”设置为“是”。

1. 若要应用通知首选项，请选择“保存”。

如果用户在执行 SSPR 过程时需要更多帮助，你可以自定义“联系管理员”链接。 用户可以在 SSPR 注册过程中，以及用户解锁帐户或重置密码时选择此链接。 为确保用户获得所需的支持，我们强烈建议提供自定义的支持电子邮件或 URL。

1. 在“自定义”页的左侧菜单中，将“自定义支持链接”设置为“是”。 
1. 在“自定义支持电子邮件或 URL”字段中提供电子邮件地址或网页 URL（例如 https:\//support.contoso.com/），通过此链接，用户可从你的组织获得更多帮助
1. 若要应用自定义链接，请选择“保存”。

## <a name="test-self-service-password-reset"></a>测试自助式密码重置

启用并配置 SSPR 后，使用在上一部分所选组（例如 Test-SSPR-Group）中的用户测试 SSPR 过程。 下面的示例使用 testuser 帐户。 提供你自己的用户帐户。 它属于本教程第一部分中为 SSPR 启用的组。

> [!NOTE]
> 测试自助式密码重置时，请使用非管理员帐户。 默认情况下，Azure AD 为管理员启用自助式密码重置。 管理员需要使用两种身份验证方法重置密码。 有关详细信息，请参阅[管理员重置策略差异](concept-sspr-policy.md#administrator-reset-policy-differences)。

1. 若要查看手动注册过程，请以 InPrivate 或无痕窗口模式打开新的浏览器窗口，并浏览到 https:\//aka.ms/ssprsetup。 Azure AD 将在用户下次登录时将其定向到此注册门户。
1. 使用非管理员测试用户（如 testuser）登录并注册身份验证方法的联系信息。
1. 完成后，选择标记为“看起来不错”的按钮并关闭浏览器窗口。
1. 在 InPrivate 或无痕窗口模式下打开新的浏览器窗口，并浏览到 https:\//aka.ms/sspr。
1. 输入非管理员测试用户的用户帐户信息（如 testuser）、来自 CAPTCHA 的字符，然后选择“下一步”。

    ![输入用户帐户信息以重置密码](media/tutorial-enable-sspr/password-reset-page.png)

1. 按验证步骤重置密码。 完成后，你会收到一条电子邮件通知，表明你的密码已重置。

## <a name="clean-up-resources"></a>清理资源

在本系列的后续教程中，你将设置密码写回。 此功能将 Azure AD SSPR 中的密码更改写回到本地 AD 环境。 若要继续学习本教程系列来设置密码写回，请不要立即禁用 SSPR。

如果你不再想要使用你在本教程中配置的 SSPR 功能，请使用以下步骤将 SSPR 状态设置为“无”：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“密码重置”。 
1. 在“属性”页的“已启用自助式密码重置”选项下，选择“无”。
1. 若要应用 SSPR 更改，请选择“保存”。

## <a name="faqs"></a>常见问题

本部分介绍管理员和最终用户尝试 SSPR 的常见问题：

- 为什么联合用户在看到 **已重置你的密码** 之后最多等待 2 分钟后，才可以使用从本地同步的密码？

  对于已同步其密码的联合用户，密码的颁发机构来源为本地。 因此，SSPR 仅更新本地密码。 每 2 分钟计划一次密码哈希同步回 Azure AD。

- 预先填充 SSPR 数据（如电话和电子邮件）的新建用户访问 SSPR 注册页时，**不要失去对帐户的访问权限！** 显示为页面的标题。 为什么预先填充 SSPR 数据的其他用户看不到该消息？

  看到此消息的用户 **不要失去对帐户的访问权限！** 是为租户配置的 SSPR/合并注册组的成员。 看不到此消息的用户 **不要失去对帐户的访问权限！** 不是 SSPR/合并注册组的一部分。

- 当某些用户进行 SSPR 过程并重置其密码时，为什么看不到密码强度指示器？

  看不到弱/强密码强度的用户已启用同步密码写回。 由于 SSPR 无法确定客户本地环境的密码策略，因此无法验证密码强弱。 

## <a name="next-steps"></a>后续步骤

在本教程中，你为选定的用户组启用了 Azure AD 自助式密码重置。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 为一组 Azure AD 用户启用自助式密码重置
> * 设置身份验证方法和注册选项
> * 以用户身份测试 SSPR 过程

> [!div class="nextstepaction"]
> [启用 Azure AD 多重身份验证](./tutorial-enable-azure-mfa.md)
