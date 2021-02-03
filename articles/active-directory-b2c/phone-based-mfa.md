---
title: 保护 Azure AD B2C 中基于手机的 MFA
titleSuffix: Azure AD B2C
description: 通过使用 Azure Monitor Log Analytics 报表和警报，了解如何使用 Azure AD B2C 租户中的 (MFA) 保护基于手机的多重身份验证的提示。 使用工作簿来识别欺诈性电话身份验证并缓解欺诈性登录。 =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca73e020009817001f309ddf29c2984a8541026
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527249"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>保护基于电话的多重身份验证 (MFA) 

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

使用 Azure Active Directory (Azure AD) 多重身份验证 (MFA) ，用户可以选择在注册进行验证的电话号码接收自动语音呼叫。 恶意用户可以通过以下方式利用此方法：创建多个帐户并拨打电话，无需完成 MFA 注册过程。 这种失败的登录可能会耗尽允许的注册尝试，阻止其他用户注册 Azure AD B2C 租户中的新帐户。 为了帮助防范这些攻击，你可以使用 Azure Monitor 来监视手机身份验证失败并缓解欺诈性登录。

## <a name="prerequisites"></a>必备条件

在开始之前，请创建 [Log Analytics 工作区](azure-monitor.md)。

## <a name="create-a-phone-based-mfa-events-workbook"></a>创建基于电话的 MFA 事件工作簿

GitHub 中的 [Azure AD B2C 报表 & 警报](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) 存储库包含可用于基于 Azure AD B2C 日志创建和发布报表、警报和仪表板的项目。 下面所示的草稿工作簿重点介绍了与电话相关的故障。

### <a name="overview-tab"></a>概述选项卡

以下信息显示在 " **概述** " 选项卡上：

- 失败原因 (每个给定原因的失败电话身份验证总数) 
- 由于信誉不良而被阻止
- 电话身份验证失败的 IP 地址 (每个给定 IP 地址的失败电话身份验证的总数) 
- 具有 IP 地址的电话号码-电话身份验证失败
- 每个客户端浏览器 (手机身份验证失败) 
- 每个客户端操作系统的操作系统 (电话身份验证失败) 

![概述选项卡](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>“详细信息”选项卡

以下信息将在 " **详细信息** " 选项卡上报告：

- Azure AD B2C 策略-电话身份验证失败
- 电话身份验证失败（按电话号码）-时间图表 (可调时间线) 
- Azure AD B2C 策略的手机身份验证失败– (可调时间线的时间图表) 
- 电话身份验证失败（按 IP 地址）-时间图表 (可调时间线) 
- 选择 "电话号码" 查看失败的详细信息 (选择电话号码以获取故障的详细列表) 

![第1个详细信息选项卡](media/phone-based-mfa/details-tab-1.png)

![详细信息选项卡第2栏](media/phone-based-mfa/details-tab-2.png)

![详细信息选项卡3（共3）](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>使用工作簿标识欺诈性登录

您可以使用该工作簿来了解基于电话的 MFA 事件，并识别可能对电话服务的恶意使用。

1. 通过回答以下问题来了解你的租户的正常情况：

   - 你需要从哪个地区进行基于手机的 MFA？
   - 检查针对失败的基于电话的 MFA 尝试显示的原因;它们被视为正常还是预期？

2. 识别欺诈注册的特征：

   - **基于位置**：按 IP 地址检查与不希望用户注册的位置相关联的任何帐户的 **电话身份验证失败** 。

   > [!NOTE]
   > 提供的 IP 地址是大致区域。

   - **基于速度**：查看 **每日失败的电话身份验证超时 ()**，表示每天发出异常的失败电话身份验证尝试的电话号码，按从最高 (左侧) 到最低 (右) 排序。

3. 按照下一部分中的步骤，缓解欺诈性登录。
 

## <a name="mitigate-fraudulent-sign-ups"></a>缓解欺诈性登录

采取以下措施来帮助减少欺诈性登录。

- 使用 **建议** 的用户流版本来执行以下操作：
     
   - [启用电子邮件一次性密码功能 (OTP) ](phone-authentication-user-flows.md) 用于 MFA (同时适用于注册和登录流) 。
   - [将条件性访问策略配置](conditional-access-identity-protection-setup.md) 为基于位置阻止登录 (仅适用于登录流，而不是) 注册流。
   - 使用 API 连接器 [与防机器人解决方案集成，例如 reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (适用于) 的注册流。

- 从下拉菜单中删除与你的组织不相关的国家/地区代码，用户在此下拉菜单中验证其电话号码 (此更改将应用于将来的登录) ：
    
   1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com)。

   2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。

   3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。

   4. 选择 "用户流"，然后选择 " **语言**"。 为组织的地理位置选择语言，打开 "语言详细信息" 面板。  (此示例中，我们将为美国) 选择 " **英语** "。 选择 " **多重身份验证" 页**，然后选择 " **(en) 下载默认值**。
 
      ![上载新替代以下载默认值](media/phone-based-mfa/download-defaults.png)

   5. 打开在上一步中下载的 JSON 文件。 在文件中，搜索 `DEFAULT` ，并将行替换为 `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` 。 请确保将设置 `Overrides` 为 `true` 。

   > [!NOTE]
   > 你可以在元素中自定义允许的国家/地区代码的列表 `countryList` (参阅) 的 [电话因素身份验证页面示例](localization-string-ids.md#phone-factor-authentication-page-example) 。

   7. 保存 JSON 文件。 在 "语言详细信息" 面板中的 " **上传新替代**" 下，选择已修改的 JSON 文件以将其上传。

   8. 关闭面板，然后选择 " **运行用户流**"。 对于本示例，请确认 **美国** 是下拉列表中唯一可用的国家/地区代码：
 
      ![国家/地区代码下拉](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>后续步骤

- 了解 [Azure AD B2C 的标识保护和条件性访问](conditional-access-identity-protection-overview.md) 

- 将 [条件性访问应用于 Azure Active Directory B2C 中的用户流](conditional-access-user-flow.md)