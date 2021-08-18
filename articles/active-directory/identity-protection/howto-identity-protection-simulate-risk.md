---
title: 在 Azure AD 标识保护中模拟风险检测
description: 了解如何在标识保护中模拟风险检测
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e610c6dd2763a029ddc0f18fe5526d77e5ff685a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733656"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>在标识保护中模拟风险检测

管理员可能希望在其环境中模拟风险，以完成以下各项：

- 通过模拟风险检测和漏洞在标识保护环境中填充数据。
- 创建基于风险的条件访问策略，并测试这些策略的影响。

本文逐步介绍了如何模拟以下风险检测类型：

- 匿名 IP 地址（简单）
- 不熟悉的登录属性（中等）
- 异常位置登录（困难）

不能以安全方式模拟其他风险检测。

若要详细了解每个风险检测，可以参阅[什么是风险](concept-identity-protection-risks.md)一文。

## <a name="anonymous-ip-address"></a>匿名 IP 地址

完成以下过程需要使用：

- [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)，用于模拟匿名 IP 地址。 如果组织限制使用 Tor 浏览器，则可能需要使用虚拟机。
- 还没有注册 Azure AD 多重身份验证的测试帐户。

**若要模拟从匿名 IP 登录，请执行以下步骤**：

1. 使用 [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
2. 输入要在 **从匿名 IP 地址登录** 报告中显示的帐户的凭据。

登录会在 10 - 15 分钟内显示在“标识保护”仪表板上。 

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登录属性

若要模拟不熟悉的位置，必须从测试帐户之前未登录过的位置和设备登录。

下面的过程使用新创建的以下项目：

- VPN 连接，用于模拟新位置。
- 虚拟机，用于模拟新设备。

完成以下过程需要使用具有以下特征的用户帐户：

- 至少 30 天的登录历史记录。
- 已启用 Azure AD 多重身份验证。

**若要模拟从不熟悉的位置登录，请执行以下步骤**：

1. 当使用测试帐户登录时，通过不通过 MFA 质询，让多重身份验证 (MFA) 质询失败。
2. 使用新 VPN 导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)，然后输入测试帐户的凭据。

登录会在 10 - 15 分钟内显示在“标识保护”仪表板上。

## <a name="atypical-travel"></a>异常位置登录

模拟异常位置登录条件很困难，因为此算法使用机器学习来剔除误报，如来自熟悉设备的异常位置登录，或来自目录中其他用户所使用的 VPN 的登录。 另外，此算法要求用户 14 天 10 次的登录历史记录，才能开始生成风险检测。 考虑到机器学习模型的复杂性以及上述规则的存在，以下步骤有可能不会导致风险检测。 你可能希望为多个 Azure AD 帐户复制这些步骤，以模拟这种检测。

若要模拟异常位置登录风险检测，请按以下步骤操作：

1. 使用标准浏览器，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。  
2. 输入要为其生成异常位置登录风险检测的帐户的凭据。
3. 更改用户代理。 可以通过开发人员工具 (F12) 更改 Microsoft Edge 中的用户代理。
4. 更改 IP 地址。 可以使用 VPN、Tor 加载项或在 Azure 中在不同的数据中心内新建虚拟机来更改 IP 地址。
5. 像往常一样使用相同凭据并在上次登录后的数分钟内登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

登录会在 2-4 小时内显示在“标识保护”仪表板中。

## <a name="testing-risk-policies"></a>测试风险策略

此部分逐步介绍了如何测试在[操作说明：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)一文中创建的用户和登录风险策略。

### <a name="user-risk-policy"></a>用户风险策略

若要测试用户风险安全策略，请执行以下步骤：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 依次转到“Azure Active Directory” > “安全性” > “概述”。
1. 选择“配置用户风险策略”。
   1. 在“分配”下
      1. 用户 - 选择“所有用户”或“选择个人和组”（如果你限制推出的话）。
         1. （可选）可以选择从策略中排除用户。
      1. “条件” - “用户风险”：Microsoft 建议将此选项设置为“高”。
   1. 在“控制”下
      1. “访问”：Microsoft 建议设置“允许访问”和“需要更改密码”。
   1. “强制执行策略” - “关”
   1. “保存”：此操作会将你返回到“概述”页。
1. 可以通过特定的方式来提升测试帐户的用户风险，例如，可以多次模拟某个风险检测。
1. 等待几分钟，然后验证用户风险是否提升。 如果否，请为用户模拟更多的风险检测。
1. 返回到风险策略，然后将“强制执行策略”设置为“开”，并保存策略更改。
1. 现在可以使用风险级别已提升的用户登录，以便测试基于用户风险的条件访问。

### <a name="sign-in-risk-security-policy"></a>登录风险安全策略

若要测试登录风险策略，请执行以下步骤：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 依次转到“Azure Active Directory” > “安全性” > “概述”。
1. 选择“配置登录风险策略”。
   1. 在“分配”下
      1. 用户 - 选择“所有用户”或“选择个人和组”（如果你限制推出的话）。
         1. （可选）可以选择从策略中排除用户。
      1. “条件” - “登录风险”：Microsoft 建议将此选项设置为“中等以上”。
   1. 在“控制”下
      1. “访问”：Microsoft 建议设置“允许访问”和“需要多重身份验证”。
   1. “强制执行策略” - “开”
   1. “保存”：此操作会将你返回到“概述”页。
1. 现在可以使用风险会话（例如，使用 Tor 浏览器）登录，以便测试基于登录风险的条件访问。 

## <a name="next-steps"></a>后续步骤

- [什么是风险？](concept-identity-protection-risks.md)

- [操作说明：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory 标识保护](overview-identity-protection.md)
