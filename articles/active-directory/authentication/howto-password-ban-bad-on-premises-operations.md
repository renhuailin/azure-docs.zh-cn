---
title: 启用本地 Azure AD 密码保护
description: 了解如何为本地 Active Directory 域服务环境中启用 Azure AD 密码保护
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741773"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>启用本地 Azure Active Directory 密码保护

用户经常创建包含本地常见字词（例如学校、运动团队或名人）的密码。 这些密码很容易猜出，对基于字典的攻击的抵御能力很弱。 若要在组织中强制实施强密码，Azure Active Directory (Azure AD) 密码保护可以提供全局、自定义受禁密码列表。 如果与受禁密码列表中的某个密码匹配，则密码更改请求将会失败。

若要保护本地 Active Directory 域服务 (AD DS) 环境，可以安装并配置 Azure AD 密码保护，使其与本地 DC 一起工作。 本文介绍如何为本地环境启用 Azure AD 密码保护。

有关 Azure AD 密码保护在本地环境中的工作原理的详细信息，请参阅[如何为 Windows Server Active Directory 强制实施 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="before-you-begin"></a>开始之前

本文介绍如何为本地环境启用 Azure AD 密码保护。 在完成本文之前，请在本地 AD DS 环境中[安装并注册 Azure AD 密码保护代理服务和 DC 代理](howto-password-ban-bad-on-premises-deploy.md)。

## <a name="enable-on-premises-password-protection"></a>启用本地密码保护

1. 登录到 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory” > “安全性” > “身份验证方法” > “密码保护”   。
1. 将“对 Windows Server Active Directory 启用密码保护”选项设置为“是”。

    如果此设置指定为“否”，则所有已部署的 Azure AD 密码保护 DC 代理将进入静止模式，在此情况下，会按原样接受所有密码。 不会执行任何验证活动，也不会生成审核事件。

1. 建议最初将“模式”设置为“审核”。 在熟悉该功能及其对组织中用户产生的影响后，可将“模式”切换为“强制”。 有关详细信息，请参阅以下有关[操作模式](#modes-of-operation)的部分。
1. 准备就绪后，选择“保存”。

    [![在 Azure 门户中的“身份验证方法”下启用本地密码保护](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>操作模式

启用本地 Azure AD 密码保护时，可以使用审核模式或强制模式 。 我们建议始终以审核模式开始进行初始的部署和测试。 然后，应该监视事件日志中的条目，以预测在启用“强制”模式后，任何现有操作过程是否受到干扰。

### <a name="audit-mode"></a>审核模式

“审核”模式是在“假设”模式下运行软件的一种方式。 每个 Azure AD 密码保护 DC 代理服务根据当前处于活动状态的策略评估传入的密码。

如果当前策略配置为审核模式，则“不合适的”密码会导致生成事件日志消息，但仍会处理并更新这些密码。 这是审核模式与强制模式之间的唯一差别。 其他所有操作的运行方式相同。

### <a name="enforced-mode"></a>强制模式

“强制”模式旨在用作最终配置。 与使用审核模式时一样，每个 Azure AD 密码保护 DC 代理服务根据当前处于活动状态的策略评估传入的密码。 不过，如果启用了强制模式，根据策略视为不安全的密码将被拒绝。

如果在强制模式下 Azure AD 密码保护 DC 代理拒绝了某个密码，最终用户将会看到在传统本地密码复杂性强制措施拒绝用户密码时显示的类似错误。 例如，用户在 Windows 登录或更改密码屏幕上可能会看到以下传统错误消息：

“无法更新密码。为新密码提供的值不符合域的长度、复杂性或历史记录要求。”

此消息仅仅是多种可能结果的一个示例。 具体的错误消息根据尝试设置不安全密码的实际软件或场景而异。

受影响的最终用户可能需要咨询其 IT 人员才能了解新的要求和选择安全密码。

> [!NOTE]
> Azure AD 密码保护无法控制在拒绝了弱密码时客户端计算机所显示的具体错误消息。

## <a name="next-steps"></a>后续步骤

若要为组织自定义受禁密码列表，请参阅[配置 Azure AD 密码保护自定义受禁密码列表](tutorial-configure-custom-password-protection.md)。

若要监视本地事件，请参阅[监视本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-monitor.md)。
