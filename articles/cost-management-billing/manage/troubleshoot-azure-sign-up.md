---
title: 排查在 Azure 门户注册新帐户时遇到的问题
description: 解决尝试在 Microsoft Azure 门户注册新帐户时遇到的问题。
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: v-miegge
ms.openlocfilehash: 0000d49d9e434a4fd07f4c3a05be60f20ebca3be
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711002"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal"></a>排查在 Azure 门户注册新帐户时遇到的问题

尝试在 Microsoft Azure 门户中注册新帐户时，可能会遇到问题。 本简短指南将指导你完成注册过程，并在每个步骤讨论一些常见问题。

> [!NOTE]
> 如果你已有一个现有帐户，并且正在寻求排查登录问题的指导，请参阅[排查 Azure 订阅登录问题](./troubleshoot-sign-in-issue.md)。

## <a name="before-you-begin"></a>开始之前

在开始注册之前，请验证以下内容：

- Azure 个人资料的信息（包括联系人电子邮件地址、街道地址和电话号码）正确无误。
- 信用卡信息正确无误。
- 你还没有具有相同信息的 Microsoft 帐户。

## <a name="guided-walkthrough-of-azure-sign-up"></a>Azure 注册的引导式演练

Azure 注册体验包含四个部分：

- 关于你
- 通过手机进行身份验证
- 通过卡进行身份验证
- 协议

本演练提供了用于注册 Azure 帐户的正确信息的示例。 此外，每个部分还包含一些常见问题及其解决方法。

## <a name="about-you"></a>关于你

初次注册 Azure 时，需要提供自己的一些信息，包括：

- 国家/地区
- 名字
- 姓氏
- 电子邮件地址
- 电话号码
- 信用卡信息
 
### <a name="common-issues-and-solutions"></a>常见问题和解决方案

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>你会看到消息“由于你的帐户存在问题，我们无法处理注册。 请联系计费支持人员”

若要解决此错误，请执行以下步骤：

1. 登录到 [Microsoft 帐户中心](https://account.microsoft.com/)。
1. 在页面顶部选择“你的信息”。
1. 验证计费和发运详细信息是否完整且有效。
1.  注册 Azure 订阅时，请验证信用卡注册的帐单邮寄地址与银行记录相符。

如果继续收到该消息，请尝试使用其他浏览器进行注册。

InPrivate 浏览如何？

#### <a name="free-trial-is-not-available"></a>免费试用版不可用

是否曾经使用过 Azure 订阅？ Azure 使用条款协议仅向 Azure 新用户授予免费试用版激活权限。 如果已拥有任何其他类型的 Azure 订阅，则无法激活免费试用版。 请考虑注册[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>你会看到消息“你不符合 Azure 订阅资格”

若要解决这一问题，请仔细检查以下项是否属实：

- 为 Azure 帐户个人资料提供的信息（包括联系电子邮件地址、街道地址和电话号码）正确无误。
- 信用卡信息正确无误。
- 你还没有使用相同信息的 Microsoft 帐户。

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>你会看到消息“你当前的帐户类型不受支持”

如果该帐户是在[非托管 Azure AD 目录](../../active-directory/enterprise-users/directory-self-service-signup.md)中注册的，并且不在你组织的 Azure AD 目录中，则可能会发生此问题。
若要解决此问题，请使用其他帐户注册 Azure 帐户，或接管非托管 AD 目录。 有关详细信息，请参阅[在 Azure Active Directory 中以管理员身份接管非托管目录](../../active-directory/enterprise-users/domains-admin-takeover.md)。

## <a name="identity-verification-by-phone"></a>通过手机进行身份验证

![通过手机进行身份验证](./media/troubleshoot-azure-sign-up/2.png)
 
收到短信或电话呼叫时，在文本框中输入你收到的代码。

### <a name="common-issues-and-solutions"></a>常见问题和解决方案

#### <a name="no-verification-text-message-or-phone-call"></a>无验证短信或电话呼叫

虽然注册验证过程通常很快，但可能需要花费长达四分钟的时间验证码才会送达。

下面是一些其他提示：

- 可以将满足要求的任意电话号码用于验证。 输入的电话号码仅用于验证，不会存储为帐户的联系号码。
  - 电话验证过程中不能使用基于 IP 的语音 (VoiP) 电话号码。
  - 检查你的手机是否可以收到从基于美国的电话号码打来的电话或发来的短信消息。
- 再次确认输入的电话号码，包括在下拉菜单中选择的国家/地区代码。
- 如果你的手机未收到短信 (SMS)，请尝试使用“呼叫我”选项。

## <a name="identity-verification-by-card"></a>通过卡进行身份验证

![通过卡进行身份验证](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>常见问题和解决方案

#### <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒绝或不被接受

虚拟或预付信用卡或借记卡不能作为 Azure 订阅有效的付款选项。 要查看可能导致卡被拒绝的其他原因，请参阅[对 Azure 注册时卡被拒绝进行故障排除](./troubleshoot-declined-card.md)。

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡窗体不支持我的帐单地址

你的帐单邮寄地址必须位于在“关于你”部分中选择的国家/地区。 验证是否选择了正确的国家/地区。

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>“使用卡进行身份验证”部分中的进度条挂起

要通过卡完成身份验证，则浏览器必须允许第三方 cookie。

使用以下步骤更新浏览器的 cookie 设置。

1. 更新 Cookie 设置。
   - 如果使用的是 **Chrome**：
     - 请依次选择“设置” > “显示高级设置” > “隐私” > “内容设置”。 清除“阻止第三方 Cookie 和站点数据”。

   - 如果使用的是 **Microsoft Edge**：
     - 请依次选择“设置” > “查看高级设置” > “Cookie” > “不阻止 Cookie”。

1. 刷新 Azure 注册页，然后检查问题是否得以解决。
1. 如果刷新后未解决该问题，请退出并重启浏览器，然后重试。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>我看到我的免费试用版帐户上产生了费用

注册后，你可能会看到你的信用卡帐户上存在少量临时验证费用。 这一费用会在 3 到 5 天内被删除。 如果你为如何管理成本而担忧，请阅读有关[分析意外费用](../understand/analyze-unexpected-charges.md)的详细信息。

## <a name="agreement"></a>协议

完成协议。

## <a name="other-issues"></a>其他问题

### <a name="cant-activate-azure-benefit-plan-like-visual-studio-bizspark-bizsparkplus-or-mpn"></a>无法激活 Visual Studio、BizSpark、BizSparkPlus 或 MPN 等 Azure 权益计划

请检查使用的是否是正确的登录凭据。 然后检查权益计划，并验证是否符合资格。
- Visual Studio
  - 在 [Visual Studio 帐户页面](https://my.visualstudio.com/Benefits)上验证资格状态。
  - 如果无法验证状态，请联系 [Visual Studio 订阅支持部门](https://visualstudio.microsoft.com/subscriptions/support/)。
- 适用于初创企业的 Microsoft
  - 登录到[适用于初创企业的 Microsoft 门户](https://startups.microsoft.com/#start-two)，验证你的资格状态，看你是否符合适用于初创企业的 Microsoft 的资格。
  - 如果无法验证你的状态，可以在[适用于初创企业的 Microsoft 论坛](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)上获取帮助。
- MPN
  - 登录到 [MPN 门户](https://mspartner.microsoft.com/Pages/Locale.aspx)，验证资格状态。 如果拥有相应的[云平台能力](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)，可能会符合其他权益的资格。
  - 如果无法验证资格状态，请联系 [MPN 支持](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)。

### <a name="cant-activate-new-azure-in-open-subscription"></a>无法激活新的 Azure 开放许可订阅

若要创建 Azure 开放许可订阅，必须具备有效的在线服务激活 (OSA) 密钥，该密钥至少有一个与之关联的 Azure 开放许可令牌。 如果没有 OSA 密钥，请联系 [Microsoft Pinpoint](https://pinpoint.microsoft.com/) 中列出的其中一个 Microsoft 合作伙伴。

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [银行卡被拒绝](./troubleshoot-declined-card.md)
- [订阅登录问题](./troubleshoot-sign-in-issue.md)
- [找不到任何订阅](./no-subscriptions-found.md)
- [企业成本视图已禁用](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 请参阅[成本管理和计费文档](../index.yml)