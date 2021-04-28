---
title: 禁用 Azure 自动化帐户托管标识（预览版）
description: 本文介绍如何禁用和删除 Azure 自动化帐户的托管标识。
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519266"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>禁用 Azure 自动化帐户托管标识（预览版）

可通过两种方式在 Azure 自动化中禁用系统分配的标识。 可从 Azure 门户完成此任务，也可使用 Azure 资源管理器 (ARM) 模板完成此任务。

## <a name="disable-managed-identity-in-the-azure-portal"></a>在 Azure 门户中禁用托管标识

无论最初如何设置托管标识，都可以从 Azure 门户禁用托管标识。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到自动化帐户，然后在“帐户设置”下选择“标识” 。

1. 将“系统分配”选项设置为“关闭”，然后按“保存”  。 当系统提示确认时，请按“是”。

托管标识已删除，不再具有对目标资源的访问权限。

## <a name="disable-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板禁用

如果使用 Azure 资源管理器模板为自动化帐户创建了托管标识，则可重用该模板并修改其设置来禁用托管标识。 将标识对象的子属性的类型设置为“无”（如以下示例所示），然后重新运行该模板。

```json
"identity": { 
   "type": "None" 
} 
```

使用这种方法删除系统分配的标识也会将它从 Azure AD 中删除。 删除分配了系统托管的标识的应用资源时，也会自动从 Azure AD 中删除这些系统分配的标识。

## <a name="next-steps"></a>后续步骤

- 有关在 Azure 自动化中启用托管标识的详细信息，请参阅[为自动化启用和使用托管标识（预览版）](enable-managed-identity-for-automation.md)。

- 有关自动化帐户安全性的概述，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。
