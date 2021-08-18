---
title: Azure Active Directory 中的密码保护
description: 了解如何通过 Azure Active Directory 密码保护动态地禁用环境中的弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1910a9c62b1d1ab6de130ace3d759243d9ff36d6
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687850"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>使用 Azure Active Directory 密码保护来消除错误密码

许多安全指南建议不要在多个位置使用同一密码，要让密码变得复杂，并避免使用简单的密码，例如 Password123。 你可以为用户提供[有关如何选择密码的指导](https://www.microsoft.com/research/publication/password-guidance)，但他们往往还是会使用弱密码或不安全的密码。 Azure AD 密码保护可以检测并阻止已知的弱密码及其变体，还可以阻止特定于你的组织的其他弱字词。

使用 Azure AD 密码保护时，默认的全局禁止密码列表会自动应用于 Azure AD 租户中的所有用户。 为了满足业务和安全需求，你可以在自定义的禁止密码列表中定义条目。 用户更改或重置密码时，系统会检查这些受禁密码列表以强制使用强密码。

你应使用其他功能（例如 [Azure AD 多重身份验证](concept-mfa-howitworks.md)），而不仅仅依赖于 Azure AD 密码保护强制实施的强密码。 有关对登录事件使用多层安全性的详细信息，请参阅[密码并不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

> [!IMPORTANT]
> 这篇概念文章向管理员介绍了 Azure AD 密码保护的工作原理。 如果你是已注册了自助式密码重置的最终用户并且需要返回到你的帐户，请转到 [https://aka.ms/sspr](https://aka.ms/sspr)。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

## <a name="global-banned-password-list"></a>全局禁止密码列表

Azure AD 标识保护团队不断分析 Azure AD 安全遥测数据，以查找常用的弱密码或已泄露的密码。 具体而言，分析查找的是常用作弱密码基础的基本字词。 如果发现弱字词，系统会将它们添加到全局受禁密码列表中。 全局受禁密码列表的内容并不基于任何外部数据源，而是基于 Azure AD 安全遥测和分析的结果。

为 Azure AD 租户中的任何用户更改或重置密码时，系统使用当前版本的全局受禁密码列表来验证密码的强度。 这种验证检查使所有 Azure AD 客户得到更强的密码。

全局受禁密码列表自动应用于 Azure AD 租户中的所有用户。 不需要启用或配置任何内容，并且无法禁用。 用户通过 Azure AD 更改或重置密码时，这个全局受禁密码列表就会应用于用户。

> [!NOTE]
> 网络罪犯在攻击中也使用类似的策略来识别常见的弱密码和变体。 为了提高安全性，Microsoft 不会发布全局受禁密码列表的内容。

## <a name="custom-banned-password-list"></a>自定义禁止密码列表

一些组织希望提高安全性，并将他们自己的自定义项添加到全局受禁密码列表的最前面。 若要添加自己的条目，可以使用自定义受禁密码列表。 添加到自定义受禁密码列表中的字词应集中于组织特定的字词，例如：

- 品牌名称
- 产品名称
- 地点，例如公司总部
- 公司特定的内部字词
- 具有特定公司含义的缩写

如果字词已被添加到自定义受禁密码列表，它们会与全局受禁密码列表中的字词结合。 然后，系统根据这些受禁密码列表的组合来验证密码更改或密码重置事件。

> [!NOTE]
> 自定义受禁密码列表限制为最多 1000 个字词。 它并非用于阻止超大的密码列表。
>
> 要充分利用自定义受禁密码列表的优势，先要了解[如何评估密码](#how-are-passwords-evaluated)，然后再向自定义受禁密码列表中添加字词。 通过此方法，可以有效检测和阻止大量弱密码及其变体。

![在“身份验证方法”下修改自定义受禁密码列表](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

假设有一个名为 Contoso 的客户。 公司总部位于伦敦，生产名为“Widget”的产品。 对于本示例中的客户而言，尝试阻止这两个单词的如下特定变体事倍功半且不够安全：

- Contoso!1
- "Contoso@London"
- ContosoWidget
- !Contoso
- LondonHQ

而仅阻止关键基本字词则更有效、更安全，例如以下关键基本字词：

- “Contoso”
- London
- Widget

随后，密码验证算法自动阻止弱变体和弱组合。

若要开始使用自定义受禁密码列表，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：配置自定义受禁密码](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密码喷射攻击和第三方的已泄露密码列表

Azure AD 密码保护可帮助你防御密码喷洒攻击。 大多数密码喷射攻击不会尝试多次攻击任何给定的帐户。 这种行为会增加被帐户锁定或其他方式检测到的可能性。

实际上，大多数密码喷射攻击仅针对企业中的每个帐户提交少量的已知最弱密码。 此方法可以让攻击者快速搜索容易受到攻击的帐户，并避免潜在的检测阈值。

Azure AD 密码保护可以有效地阻止可能在密码喷洒攻击中使用的所有已知弱密码。 此保护根据来自 Azure AD 的实际安全遥测数据生成全局受禁密码列表。

有一些第三方网站列举了在之前已公开的安全漏洞中遭到泄露的数百万个密码。 第三方密码验证产品通常基于针对这数百万个密码进行的暴力比较。 但是，考虑到密码喷射攻击者使用的典型策略，这些技术并不是提高密码整体强度的最佳方法。

> [!NOTE]
> 全局受禁密码列表不基于任何第三方数据源，包括已泄露密码的列表。

尽管全局受禁密码列表与一些第三方的大容量列表相比很小，但它源自对实际密码喷射攻击的实际安全遥测。 此方法提高了整体安全性和有效性，密码验证算法也使用了智能模糊匹配技术。 因此，Azure AD 密码保护能够有效地检测和阻止在企业中使用这数百万个最常见的弱密码。

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

许多组织都有混合标识模型，其中包括本地 Active Directory 域服务 (AD DS) 环境。 如果要将 Azure AD 密码保护的安全性优势扩展到 AD DS 环境中，你可以在本地服务器上安装组件。 这些代理需要本地 AD DS 环境中的密码更改活动符合 Azure AD 中的密码策略。

有关详细信息，请参阅[对 AD DS 强制执行 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="how-are-passwords-evaluated"></a>如何评估密码

当用户更改或重置其密码时，系统通过以下方式检查新密码的强度和复杂程度：对照全局和自定义受禁密码列表中的字词组合列表执行验证。

即使用户的密码包含受禁密码，但如果整体密码足够强，仍可能会接受该密码。 将通过以下步骤评估新配置的密码的整体强度，从而确定应该接受还是拒绝该密码：

### <a name="step-1-normalization"></a>步骤 1：规范化

新密码首先经过一个规范化处理过程。 此技术可以将一小组受禁密码映射到一组大得多的潜在弱密码。

规范化分为以下两部分：

* 将所有大写字母都改为小写字母。
* 然后，替换常见的字符，如下例中所示：

   | 原始字母 | 替换后的字母 |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

请看下面的示例：

* 禁止使用密码“blank”。
* 用户尝试将密码更改为“Bl@nK”。
* 即使并不禁止使用“Bl@nk”，规范化过程也会将这个密码转换为“blank”。
* 因此，该密码会被拒绝。

### <a name="step-2-check-if-password-is-considered-banned"></a>步骤 2：检查密码是否被视为受禁密码

接下来，对密码执行其他匹配行为方面的检查，并生成分数。 根据这个最终分数来确定是接受还是拒绝密码更改请求。

#### <a name="fuzzy-matching-behavior"></a>模糊匹配行为

模糊匹配用于经过规范化处理的密码，可以识别它是否包含在全局禁止列表或自定义禁止密码列表中找到的密码。 匹配过程根据编辑距离为一 (1) 来比较。

请看下面的示例：

* 禁止使用密码“abcddf”。
* 用户尝试将密码更改为以下某个密码：

   * abcdeg - 最后一个字符从 f 改为 g
   * abcdefg - 在末尾追加了 g
   * abcde - 从末尾删除了最后的 f

* 上述每个密码都不与受禁密码“abcdef”精确匹配。

    但是每个示例与受禁字词“abcdef”的编辑距离都为 1，所以它们都被视为与“abcdef”匹配。
* 因此，这些密码会被拒绝。

#### <a name="substring-matching-on-specific-terms"></a>子字符串匹配（特定字词）

对经过规范化处理的密码使用子字符串匹配，以检查用户的名字、姓氏以及租户名称。 为本地混合方案验证 AD DS 域控制器上的密码时，不执行租户名匹配。

> [!IMPORTANT]
> 仅对长度至少为四个字符的名称和其他字词强制执行子字符串匹配。

请看下面的示例：

* 一位名为 Poll 的用户想将密码重置为“p0LL23fb”。
* 经过规范化处理后，此密码将变为“poll23fb”。
* 子字符串匹配发现该密码包含用户的名字“Poll”。
* 即使两个受禁密码列表中都未专门列出“poll23fb”，但子字符串匹配在密码中发现了“Poll”。
* 因此，该密码会被拒绝。

#### <a name="score-calculation"></a>分数计算

下一步在用户经过规范化的新密码中识别禁止密码的所有实例。 分数是根据以下标准分配的：

1. 对用户密码中找到的每个受禁密码都会给一分。
1. 余下的不属于受禁密码的每个字符给一分。
1. 密码的分数必须至少为五 (5) 分才能被接受。

在接下来的两个示例场景中，假设 Contoso 正在使用 Azure AD 密码保护，并且其自定义受禁密码列表中包含“contoso”。 我们还假设全局受禁密码列表中包含“blank”。

在下面的示例场景中，用户将密码更改为“C0ntos0Blank12”：

* 经过规范化处理后，此密码变为“contosoblank12”。
* 匹配过程发现此密码包含两个受禁密码：“contoso”和“blank”。
* 然后为此密码给出以下分数：

    [contoso] + [blank] + [1] + [2] = 4 分

* 此密码低于五 (5) 分，因此被拒绝。

接下来，我们通过一个稍微不同的示例来演示密码应具有怎样的额外复杂程度才能得出使密码被接受所需的分数。 在下面的示例场景中，用户将密码更改为“ContoS0Bl@nkf9!”：

* 经过规范化处理后，此密码变为“contosoblankf9!”。
* 匹配过程发现此密码包含两个受禁密码：“contoso”和“blank”。
* 然后为此密码给出以下分数：

    [contoso] + [blank] + [f] + [9] + [!] = 5 分

* 此密码达到了五 (5) 分的最低限度，因此会被接受。

> [!IMPORTANT]
> 根据持续的安全性分析和研究，在 Azure 中受禁密码算法以及全局受禁密码列表可能随时会更改。
>
> 对于混合方案中的本地 DC 代理服务，只有在升级 DC 代理软件后，更新的算法才能生效。

## <a name="what-do-users-see"></a>用户看到什么

用户尝试将密码重置为某些会被禁止的密码时，系统显示以下错误消息：

“很遗憾，密码中包含的单词、短语或模式使密码很容易被猜出。请使用其他密码重试。”

## <a name="license-requirements"></a>许可要求

| 用户 | 带有全局禁止密码列表的 Azure AD 密码保护 | 带有自定义禁止密码列表的 Azure AD 密码保护|
|-------------------------------------------|---------------------------|---------------------------|
| 仅限云的用户                          | Azure AD Free             | Azure AD Premium P1 或 P2 |
| 从本地 AD DS 同步的用户 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 未同步到 Azure AD 的本地 AD DS 用户也会受益于基于已同步用户现有许可的 Azure AD 密码保护。

可以在 [Azure Active Directory 定价站点](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)上找到包括成本在内的其他许可信息。

## <a name="next-steps"></a>后续步骤

若要开始使用自定义受禁密码列表，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：配置自定义受禁密码](tutorial-configure-custom-password-protection.md)

接下来，你还可以[启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)。
