---
title: 在 Azure 自动化中禁用本地身份验证
description: 本文介绍如何在 Azure 自动化中禁用本地身份验证。
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 29caef661b7078844991c3ca3867a1f03eb0114e
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293503"
---
# <a name="disable-local-authentication-in-automation"></a>在自动化中禁用本地身份验证

Azure 自动化为所有自动化服务公共终结点提供 Microsoft Azure Active Directory (Azure AD) 身份验证支持。 此项关键安全增强消除了证书依赖关系，使组织可以通过控制措施来禁用本地身份验证方法。 需要通过 Azure AD 集中控制和管理标识与资源凭据时，此功能可为你提供无缝集成。

Azure 自动化通过 Azure 策略[配置 Azure 自动化帐户以禁用本地身份验证](../automation/policy-reference.md#azure-automation)在自动化帐户级别提供“禁用本地身份验证”可选功能。 默认情况下，此标志在帐户级别设置为 false，因此你既可以使用本地身份验证，也可以使用 Azure AD 身份验证。 如果你选择禁用本地身份验证，则自动化服务仅接受基于 Azure AD 的身份验证。

如果已禁用身份验证，你可能会在 Azure 门户中的登陆页上收到一条针对所选自动化帐户的警告消息。若要确认是否已启用本地身份验证策略，请使用 PowerShell cmdlet [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) 并检查属性 `DisableLocalAuth`。 值为 `true` 表示已禁用本地身份验证。

禁用本地身份验证的操作不会立即生效。 请等待几分钟，使服务阻止将来的身份验证请求。
 
## <a name="re-enable-local-authentication"></a>重新启用本地身份验证

若要重新启用本地身份验证，请结合 `-DisableLocalAuth false` 参数执行 PowerShell cmdlet `Set-AzAutomationAccount`。等待几分钟，使服务接受更改以允许本地身份验证请求。 

## <a name="compatibility"></a>兼容性

下表描述了通过禁用本地身份验证阻止其工作的行为或功能。

|方案 | 替代方法 |
|---|---|
|使用 Webhook 启动 Runbook。 | 通过 Azure 资源管理器模板启动使用 Azure AD 身份验证的 Runbook 作业。 |
|使用自动化 Desired State Configuration。| 使用 [Azure Policy 来宾配置](/governance/policy/concepts/guest-configuration)。  |
|使用基于代理的混合 Runbook 辅助角色。| 使用[基于扩展的混合 Runbook 辅助角色（预览版）](./extension-based-hybrid-runbook-worker-install.md)。|

## <a name="limitations"></a>限制

禁用本地身份验证后，更新管理修补将无法正常工作。


## <a name="next-steps"></a>后续步骤
- [Azure 自动化帐户身份验证概述](./automation-security-overview.md)