---
title: 保护 Azure AD B2C 中基于电话的 MFA
titleSuffix: Azure AD B2C
description: 了解有关通过使用 Azure Monitor Log Analytics 报表和警报，保护 Azure AD B2C 租户中基于电话的多重身份验证 (MFA) 的提示。 使用工作簿识别欺诈性电话身份验证并减少欺诈性登录。 =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 112fe68a1d8682b03a9f08839d827b71ebadf1d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571762"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>保护基于电话的多重身份验证 (MFA)

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

使用 Azure Active Directory (Azure AD) 多重身份验证 (MFA) 时，用户可以选择通过注册进行验证的电话号码接收自动语音呼叫。 存有恶意的用户就会利用此方法，创建多个帐户并拨打电话，却不完成 MFA 注册过程。 连续多次注册失败达到允许的注册尝试次数上限后，将导致其他用户也无法在 Azure AD B2C 租户中注册新帐户。 为了帮助防范这些攻击，可以使用 Azure Monitor 来监视电话身份验证失败并减少欺诈性注册。

## <a name="prerequisites"></a>先决条件

在开始之前，请先创建一个 [Log Analytics 工作区](azure-monitor.md)。

## <a name="create-a-phone-based-mfa-events-workbook"></a>创建基于电话的 MFA 事件工作簿

GitHub 中的 [Azure AD B2C 报表和警报](https://github.com/azure-ad-b2c/siem#phone-authentication-failures)存储库中包含了若干项目，在基于 Azure AD B2C 日志创建和发布报表、警报和仪表板时可以使用。 下图所示的草稿工作簿重点介绍了与电话相关的失败情况。

### <a name="overview-tab"></a>概述选项卡

以下信息显示在“概述”选项卡上：

- 失败原因（每个给定原因下电话身份验证失败的总数）
- 由于信誉不良而被阻止
- 电话身份验证失败的 IP 地址（每个给定 IP 地址下电话身份验证失败的总数）
- 存在“IP 地址 - 电话身份验证失败”的电话号码
- 浏览器（每个客户端浏览器上的电话身份验证失败）
- 操作系统（每个客户端操作系统上的电话身份验证失败）

![概述选项卡](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>“详细信息”选项卡

以下信息将在“详细信息”选项卡上报告：

- Azure AD B2C 策略 - 失败的电话身份验证
- 电话身份验证失败（按电话号码）- 时间图（可调时间线）
- 电话身份验证失败（按 Azure AD B2C 策略）- 时间表（可调时间线）
- 电话身份验证失败（按 IP 地址）- 时间图（可调时间线）
- 选择电话号码以查看失败的详细信息（选择电话号码以获取失败的详细列表）

![“详细信息”选项卡 1（共 3 个）](media/phone-based-mfa/details-tab-1.png)

![“详细信息”选项卡 2（共 3 个）](media/phone-based-mfa/details-tab-2.png)

![“详细信息”选项卡 3（共 3 个）](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>使用工作簿来识别欺诈性注册

可以使用工作簿来了解基于电话的 MFA 事件，并识别可能存在恶意的电话服务使用。

1. 通过回答以下问题，了解租户的正常情况：

   - 希望从哪些区域进行基于电话的 MFA？
   - 检查基于电话的 MFA 尝试失败所显示的原因；是否正常或在意料中？

2. 识别欺诈性注册的特征：

   - **基于位置**：检查“电话身份验证失败(按 IP 地址)”，查找是否有任何与不希望用户注册的位置相关联的帐户。

   > [!NOTE]
   > 提供的 IP 地址是大致区域。

   - **基于速度**：查看“近期失败的身份验证(按天)”，这其中指示了每天拨打异常号码，导致电话身份验证尝试失败的电话号码，按照从最高（左）到最低（右）的顺序排列。

3. 按照下一部分中的步骤，减少欺诈性注册。
 

## <a name="mitigate-fraudulent-sign-ups"></a>减少欺诈性注册

采取以下措施，帮助减少欺诈性注册。

- 使用“推荐”版本的用户流，执行以下操作：
     
   - [启用电子邮件一次性密码功能 (OTP)](phone-authentication-user-flows.md) 进行 MFA（同时适用于注册和登录流）。
   - [配置条件访问策略](conditional-access-user-flow.md)以阻止基于位置的登录（仅适用于登录流，却不适用于注册流）。
   - 使用 API 连接器[与 reCAPTCHA 之类的防机器人解决方案相集成](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha)（适用于注册流）。

- 从用户验证电话号码的下拉菜单中删除与组织不相关的国家/地区代码（此更改将应用于未来注册）：
    
   1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com)。
   1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
   1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
   1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
   1. 选择用户流，然后选择“语言”。 选择组织的地理位置所对应的语言，以打开语言详细信息窗格。 （在本示例中，对“美国”选择“英语(en)”）。 选择“多重身份验证页”，然后选择“下载默认值(en)”。
 
      ![上传新的重写项，以下载默认值](media/phone-based-mfa/download-defaults.png)

   1. 打开上一步中下载的 JSON 文件。 在文件中，搜索 `DEFAULT` 并将行替换为 `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"`。 务必将 `Overrides` 设置为 `true`。

   > [!NOTE]
   > 可以自定义 `countryList` 元素中允许的国家/地区代码的列表（请参阅[电话因素身份验证页示例](localization-string-ids.md#phone-factor-authentication-page-example)）。

   1. 保存 JSON 文件。 在语言详细信息窗格中的“上传新的重写项”下，选择已修改的 JSON 文件以将其上传。
   1. 关闭窗口，然后选择“运行用户流”。 在本示例中，请确认下拉列表中仅显示一个国家/地区代码“美国”：
 
      ![国家/地区代码下拉菜单](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>后续步骤

- 了解[针对 Azure AD B2C 的标识保护和条件访问](conditional-access-identity-protection-overview.md) 

- 应用[对 Azure Active Directory B2C 中用户流的条件访问](conditional-access-user-flow.md)