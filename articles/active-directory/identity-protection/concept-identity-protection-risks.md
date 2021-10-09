---
title: 什么是风险？ Azure AD 标识保护
description: Azure AD 标识保护中的风险说明
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f59d127342eabf3b951972a8d9d2bd82e0a6fa0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622068"
---
# <a name="what-is-risk"></a>什么是风险？

Azure AD 标识保护中的风险检测包括已识别的与目录中的用户帐户相关的任何可疑操作。 风险检测（与用户和登录关联的检测）影响“风险用户”报告中发现的总体用户风险分数。

标识保护向组织提供强大资源的访问权限，可快速查看和响应这些可疑操作。 

![显示有风险的用户和登录的安全概述](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> 仅当使用正确的凭据时，标识保护才会生成风险检测。 如果在登录时使用错误的凭据，不表示存在凭据泄露的风险。

## <a name="risk-types-and-detection"></a>风险类型和检测

有两个级别的风险检测（用户和登录）和两种类型的检测或计算（实时和脱机）   。

实时检测可能在 5 至 10 分钟内不会显示在报告中。 脱机检测可能在 48 小时内不会显示在报告中。

### <a name="user-linked-detections"></a>与用户关联的检测

对于未关联到特定恶意登录而是与用户本身关联的用户，可以检测到其风险活动。 这些风险检测通过 Microsoft 的内部和外部威胁情报源（如 Microsoft 安全研究人员、执法人员、安全团队以及其他受信任的来源）进行脱机计算。

这些风险通过 Microsoft 的内部和外部威胁情报源（包括 Microsoft 安全研究人员、执法人员、安全团队以及其他受信任的来源）进行脱机计算。

| 风险检测 | 说明 |
| --- | --- |
| 凭据泄露 | 此风险检测类型指示用户的有效凭据已泄露。 当网络犯罪分子泄露合法用户的有效密码时，他们通常会共享这些凭据。 共享方式通常是将凭据公开发布在暗网或粘贴网站上，或者在黑市上交易或出售凭据。 当 Microsoft 凭据泄露服务从暗网、粘贴网站或其他来源获取用户凭据时，会根据 Azure AD 用户当前的有效凭据对其进行检查，找到有效的匹配项。 有关凭据泄露的详细信息，请查看[常见问题](#common-questions)。 |
| Azure AD 威胁智能 | 此风险检测类型指示用户活动对于给定用户来说是不寻常的，或者根据 Microsoft 内部和外部威胁智能源与已知攻击模式相一致。 |

### <a name="sign-in-risk"></a>登录风险

登录风险表示给定身份验证请求未经标识所有者授权的概率。 

这些风险通过 Microsoft 的内部和外部威胁情报源（包括 Microsoft 安全研究人员、执法人员、Microsoft 安全团队以及其他受信任的来源）进行实时计算或脱机计算。

| 风险检测 | 检测类型 | 说明 |
| --- | --- | --- |
| 匿名 IP 地址 | 实时 | 此风险检测类型指示从匿名 IP 地址登录（例如 Tor 浏览器或匿名 VPN）。 这些 IP 地址通常由希望隐藏其登录遥测数据（IP 地址、位置、设备等）的参与者使用以实现潜在恶意目的。 |
| 异常位置登录 | 脱机 | 此风险检测类型可标识从相距遥远的地理位置进行的两次登录，根据用户以往的行为，其中至少有一个位置属于异常。 除了若干其他因素外，此机器学习算法还考虑两次登录之间相隔的时间以及用户从第一个位置前往第二个位置所需的时间，因为这指示有不同的用户在使用相同的凭据。 <br><br> 此算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 系统最早有一个 14 天或 10 次登录的初始学习期限，在此期间它将学习新用户的登录行为。 |
| 异常令牌 | 脱机 | 此检测指示令牌中存在异常特征，例如异常令牌生存期或从不熟悉的位置播放的令牌。 此检测涵盖会话令牌和刷新令牌。 |
| 令牌颁发者异常 | 脱机 |此风险检测指示关联 SAML 令牌的 SAML 令牌颁发者可能遭到入侵。 令牌中包含的声明异常或匹配已知的攻击者模式。 |
| 受恶意软件感染的 IP 地址 | 脱机 | 此风险检测类型指示从感染了恶意软件的 IP 地址（即主动与僵尸服务器通信）登录。 该检测通过分析用户设备的 IP 地址与僵尸服务器活动时连接过僵尸服务器的 IP 地址之间的相互关系进行确定。 |
| 可疑浏览器 | 脱机 | 可疑浏览器检测指示基于同一浏览器中不同国家/地区多个租户的可疑登录活动的异常行为。 |
| 不熟悉的登录属性 | 实时 | 此风险检测类型根据登录历史记录（IP、纬度/经度和 ASN）来查找匿名登录。系统会存储用户以前的登录位置信息，并将其视为“熟悉”位置。 当从尚未在熟悉位置列表中列出的位置登录时，将触发此风险检测。 新创建的用户将在一段时间内处于“学习模式”，在此期间，当我们的算法学习用户的行为时，将关闭不熟悉的登录属性风险检测。 学习模式持续时间是动态的，取决于算法收集足够的用户登录模式信息所需的时间。 最短持续时间为五天。 用户可以在长时间处于非活动状态后返回到学习模式。 系统还会忽略从常用设备和接近熟悉位置的地理位置进行登录。 <br><br> 我们还对基本身份验证（或旧版协议）运行此检测。 由于这些协议没有新型属性（如客户端 ID），因此用来减少误报的遥测数据较为有限。 建议客户采用新式身份验证。 <br><br> 在交互式和非交互式登录中都可以检测到不熟悉的登录属性。当在非交互式登录时检测到此检测时，由于令牌重放攻击的风险，因此值得加强审查。  |
| 管理员确认用户遭入侵 | 脱机 | 此检测表明管理员已通过风险用户 UI 或 riskyUsers API 选择了“确认用户遭入侵”。 若要查看哪位管理员确认了此用户遭入侵，请（通过 UI 或 API）检查用户的风险历史记录。 |
| 恶意 IP 地址 | 脱机 | 此检测表明登录是通过恶意 IP 地址进行的。 由于从 IP 地址或其他 IP 信誉源接收到无效的凭据，因此会根据高失败率将 IP 地址视为恶意。 |
| 可疑的收件箱操作规则 | 脱机 | 此检测由 [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) 发现。 这项检测会对环境进行分析，并在用户收件箱中设置删除或移动邮件或文件夹的可疑规则时触发警报。 该检测可能表明用户帐户已遭入侵、消息被故意隐藏且邮箱被人用来在组织中分发垃圾邮件或恶意软件。 |
| 密码喷射 | 脱机 | 密码喷射攻击是指使用常见密码以统一的暴力攻击方式攻击多个用户名以获取未经授权的访问。 实施密码喷射攻击时，会触发此风险检测。 |
| Impossible travel | 脱机 | 此检测由 [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel) 发现。 此项检测可识别来源于保持一定地理距离的位置的两个用户活动（一个或多个会话），而完成这两个活动的时间段短于该用户从第一个位置旅行到第二个位置所需的时间，这表示另一个用户在使用相同的凭据。 |
| 新国家/地区 | 脱机 | 此检测由 [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country) 发现。 此项检测考虑过去的活动位置，以确定新的和不常见的位置。 异常情况检测引擎将存储组织中用户以往用过的位置的相关信息。 |
| 来自匿名 IP 地址的活动 | 脱机 | 此检测由 [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses) 发现。 此项检测可以识别使用已标识为匿名代理 IP 地址的 IP 地址展开活动的用户。 |
| 可疑收件箱转发 | 脱机 | 此检测由 [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding) 发现。 此检测查找可疑的电子邮件转发规则，例如，如果用户创建了将所有电子邮件副本转发到外部地址的收件箱规则。 |
| Azure AD 威胁智能 | 脱机 | 此风险检测类型指示给定用户的异常登录活动，或与基于 Microsoft 内部和外部威胁情报源的已知攻击模式一致的登录活动。 |

### <a name="other-risk-detections"></a>其他风险检测

| 风险检测 | 检测类型 | 说明 |
| --- | --- | --- |
| 检测到其他风险 | 实时或脱机 | 此检测表明已检测到上述某个高级检测。 高级检测仅对 Azure AD Premium P2 客户可见，因此对于没有 Azure AD Premium P2 许可证的客户，标题显示“检测到额外风险”。 |

## <a name="common-questions"></a>常见问题

### <a name="risk-levels"></a>风险级别

标识保护将风险分为三个级别：低、中和高。 配置[自定义标识保护策略](./concept-identity-protection-policies.md#custom-conditional-access-policy)时，还可将其配置为在“无风险”级别触发。 “无风险”是指没有用户标识已遭入侵的有效指示。

虽然 Microsoft 不提供有关风险计算方式的具体详细信息，但可以说每个级别在用户或登录受到攻击时都提供了更高的置信度。 例如，与某用户的一个实例出现不熟悉的登录属性相比，将凭据泄露给另一个用户所带来的威胁性更高。

### <a name="password-hash-synchronization"></a>密码哈希同步

凭据泄露等风险检测要求存在密码哈希才能进行检测。 有关密码哈希同步的详细信息，请查看[使用 Azure AD Connect 同步实现密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)一文。

### <a name="leaked-credentials"></a>凭据泄露

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Microsoft 在哪里查找凭据泄露？

Microsoft 在各种位置查找凭据泄露，包括：

- 公共粘贴网站（例如 pastebin.com 和 paste.ca），不良参与者常会在此粘贴这类信息。 此位置是大多数不良参与者查找盗用凭据的第一站。
- 执法机构。
- Microsoft 的其他小组开展暗网研究。

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>为什么看不到任何凭据泄露？

每当 Microsoft 发现新的公开可用的批处理时，都会处理凭据泄露情况。 由于其敏感性质，泄露的凭据会在处理后很快删除。 只有在启用密码哈希同步 (PHS) 后找到的新泄露的凭据将针对你的租户进行处理。 不会对先前找到的凭据对执行验证。 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>已经很长时间没有看到任何凭据泄露风险事件了？

如果未看到任何凭据泄露风险事件，原因如下：

- 没有为租户启用 PHS。
- Microsoft 找不到任何与你的用户匹配的泄露凭据对。

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Microsoft 多久处理一次新凭据？

凭据会在找到后立即处理，通常每天分多批处理。

### <a name="locations"></a>位置

风险检测中的位置由 IP 地址查找决定。

## <a name="next-steps"></a>后续步骤

- [可用于规避风险的策略](concept-identity-protection-policies.md)
- [安全性概述](concept-identity-protection-security-overview.md)
