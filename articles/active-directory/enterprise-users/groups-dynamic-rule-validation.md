---
title: 验证动态组成员资格的规则（预览版）- Azure AD | Microsoft Docs
description: 如何测试成员是否满足 Azure Active Directory 中动态组的成员身份规则。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4d4e87464012d96650a46b93f2bd7d664a5ec03
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426609"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>在 Azure Active Directory 中验证动态组成员身份规则（预览版）

Azure Active Directory (Azure AD) 现在提供验证动态组规则的方法（以公共预览版提供）。 在“验证规则”选项卡上，可以对示例组成员验证动态规则，以确认规则按预期方式工作。 创建或更新动态组规则时，管理员希望了解用户或设备是否将成为该组的成员。 这有助于评估用户或设备是否满足规则条件，并有助于在不希望其具有成员身份时进行故障排除。

## <a name="step-by-step-walk-through"></a>分步演练

首先，请转到“Azure Active Directory” > “组”。 选择现有动态组，或创建新的动态组，并单击“动态成员身份规则”。 然后，你可以查看“验证规则”选项卡。

![找到“验证规则”选项卡，并从现有规则开始](./media/groups-dynamic-rule-validation/validate-tab.png)

在“验证规则”选项卡上，可以选择用户来验证其成员身份。 一次可以选择 20 个用户或设备。

![添加要验证是否满足现有规则的用户](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

从选取器选择用户或设备并单击“选择”后，将自动开始验证并显示验证结果。

![查看规则验证的结果](./media/groups-dynamic-rule-validation/validate-tab-results.png)

结果指示用户是否为组的成员。 如果规则无效或出现网络问题，则结果将显示为“未知”。 如果显示“未知”，详细的错误消息将说明问题和所需的操作。

![查看规则验证结果的详细信息](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

你可以修改规则，这样做将触发成员身份验证。 若要查看用户不是组成员的原因，请单击“查看详细信息”，验证详细信息将显示构成规则的每个表达式的结果。 单击“确定”退出。

## <a name="next-steps"></a>后续步骤

- [组的动态成员身份规则](groups-dynamic-membership.md)
