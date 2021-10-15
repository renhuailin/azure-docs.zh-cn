---
title: Azure AD Connect 的选择性密码哈希同步
description: 本文介绍如何设置和配置要用于 Azure AD Connect 的选择性密码哈希同步。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbdc6a28af9bab40ede4f6a3a065b3ee776cf29
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235395"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>为 Azure AD Connect 配置选择性密码哈希同步

[密码哈希同步](whatis-phs.md)是用于实现混合标识的登录方法之一。 Azure AD Connect 将用户密码的哈希从本地 Active Directory 实例同步到基于云的 Azure AD 实例。  默认情况下，在设置密码哈希同步后，将对要同步的所有用户执行密码哈希同步。

如果要排除一部分用户，使其无需将密码哈希同步到 Azure AD，可以使用本文中提供的指导步骤来配置选择性密码哈希同步。

> [!IMPORTANT]
> Microsoft 不支持在正式记录的配置或操作之外修改或操作 Azure AD Connect 同步。 其中的任何配置或操作都可能会导致 Azure AD Connect 同步出现不一致或不受支持状态。因此，Microsoft 无法保证我们能够为此类部署提供有效的技术支持。

## <a name="consider-your-implementation"></a>考虑实现

为了降低配置管理工作量，你首先应该考虑要从密码哈希同步中排除的用户对象的数量。 验证以下互斥的方案中有哪些符合你的要求，以便为你选择适当的配置选项。
- 如果要 **排除** 的用户数 **少于** 要 **包含** 的用户数，请按照此 [部分](#excluded-users-is-smaller-than-included-users)中的步骤操作。
- 如果要 **排除** 的用户数 **多于** 要 **包含** 的用户数，请按照此 [部分](#excluded-users-is-larger-than-included-users)中的步骤操作。

> [!IMPORTANT]
> 选择任何一个配置选项后，应用更改所需的初始同步（完全同步）将在下一个同步周期自动执行。

> [!IMPORTANT]
> 配置选择性密码哈希同步会直接影响密码写回。 仅当用户在密码哈希同步范围内时，在 Azure Active Directory 中发起的密码更改或密码重置才会写回本地 Active Directory。

### <a name="the-admindescription-attribute"></a>AdminDescription 特性

这两种方案都依赖于将用户的 adminDescription 特性设置为特定值。  这样将应用规则，也将导致选择性 PHS 生效。

|方案|adminDescription 值|
|-----|-----|
|排除的用户少于包含的用户|PHSFiltered|
|排除的用户多于包含的用户|PHSIncluded|

可通过以下方式设置此特性：

- 使用 Active Directory 用户和计算机 UI
- 使用 `Set-ADUser` PowerShell cmdlet。  有关详细信息，请参阅 [Set-ADUser](/powershell/module/activedirectory/set-aduser)。

### <a name="disable-the-synchronization-scheduler"></a>禁用同步计划程序：

在启动任一方案之前，在对同步规则进行更改时必须禁用同步计划程序。
 1. 启动 Windows PowerShell 输入。

     `set-adsyncscheduler-synccycleenabled$false`

2. 运行以下 cmdlet，确认是否已禁用计划程序：

    `get-adsyncscheduler`

有关计划程序的详细信息，请参阅 [Azure AD Connect 同步计划程序](how-to-connect-sync-feature-scheduler.md)。

## <a name="excluded-users-is-smaller-than-included-users"></a>排除的用户少于包含的用户

以下部分介绍当要 **排除** 的用户数 **少于** 要 **包含** 的用户数时，如何启用选择性密码哈希同步。

> [!IMPORTANT]
> 在继续操作之前，请确保禁用同步计划程序，如上文所述。

- 创建 In from AD – User AccountEnabled 的可编辑副本，取消选中启用密码哈希同步的选项，并定义其范围筛选器
- 创建默认 In from AD – User AccountEnabled 的另一个可编辑副本，选中启用密码哈希同步的选项，并定义其范围筛选器
- 重新启用同步计划程序
- 在 Active Directory 中设置特性值，该特性值定义为要在密码哈希同步中允许的用户的范围特性。

> [!IMPORTANT]
> 为配置选择性密码哈希同步而提供的步骤仅影响在 Active Directory 中使用值 PHSFiltered 填充属性 adminDescription 的用户对象 。
如果未填充此特性，或者值不是 **PHSFiltered**，则这些规则将不会应用于用户对象。

### <a name="configure-the-necessary-synchronization-rules"></a>配置必要的同步规则：

 1. 启动“同步规则编辑器”，并将筛选器“密码同步”设置为“开”，将“规则类型”设置为“标准”。
     ![启动同步规则编辑器](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. 为要将选择性密码哈希同步设置为开的 Active Directory 林 Connector 选择规则 In from AD – User AccountEnabled，并单击“编辑”。 在下一个对话框中选择“是”，以创建原始规则的可编辑副本。
     ![选择规则](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. 第一个规则将禁用密码哈希同步。为新的自定义规则提供以下名称：**In from AD - User AccountEnabled - Filter Users from PHS**。
 将优先级值更改为小于 100 的数字（例如 **90** 或环境中可用的最小值）。
 请确保未选中“启用密码同步”和“已禁用”复选框。
 单击“下一步”。
  ![编辑入站规则](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. 在“范围筛选器”中，单击“添加子句”。
 在特性列中选择“adminDescription”，在“运算符”列中选择“EQUAL”，并输入 **PHSFiltered** 作为值。
     ![范围筛选器](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. 不需要进行其他更改。 “联接规则”和“转换”应保留默认的复制设置，因此你可以立即单击“保存”。
 在警告对话框中单击“确定”，该警告通知将在连接器的下一个同步周期运行完全同步。
     ![保存规则](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. 接下来，创建启用了密码哈希同步的另一个自定义规则。 为要将选择性密码哈希同步设置为开的 Active Directory 林再次选择默认规则 In from AD – User AccountEnabled，并单击“编辑”。 在下一个对话框中选择“是”，以创建原始规则的可编辑副本。
     ![自定义规则](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. 为新的自定义规则提供以下名称：**In from AD - User AccountEnabled - Users included for PHS**。
 将优先级值更改为一个小于之前创建的规则的数字（在此示例中为 89）。
 请确保选中复选框“启用密码同步”，并取消选中“禁用”复选框。
 单击“下一步”。  
     ![编辑新规则](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. 在“范围筛选器”中，单击“添加子句”。
 在特性列中选择“adminDescription”，在“运算符”列中选择“NOTEQUAL”，并输入 **PHSFiltered** 作为值。
     ![范围规则](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. 不需要进行其他更改。 “联接规则”和“转换”应保留默认的复制设置，因此你可以立即单击“保存”。
 在警告对话框中单击“确定”，该警告通知将在连接器的下一个同步周期运行完全同步。
     ![联接规则](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. 确认创建规则。 删除筛选器“密码同步”中的“开”和“规则类型”中的“标准”。 你会看到刚创建的两个新规则。
     ![确认规则](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png)

### <a name="re-enable-synchronization-scheduler"></a>重新启用同步计划程序：

完成配置所需同步规则的步骤后，请使用以下步骤重新启用同步计划程序：
 1. 在 Windows PowerShell 中，运行：

     `set-adsyncscheduler-synccycleenabled$true`

 2. 然后运行以下内容，确认它已成功启用：

     `get-adsyncscheduler`

有关计划程序的详细信息，请参阅 [Azure AD Connect 同步计划程序](how-to-connect-sync-feature-scheduler.md)。

### <a name="edit-users-admindescription-attribute"></a>编辑用户 **adminDescription** 特性：

完成所有配置后，需要在 Active Directory 中为要在密码哈希同步中 **排除** 的所有用户编辑特性 **adminDescription**，并添加在范围筛选器中使用的字符串：**PHSFiltered**。

  ![编辑属性](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

还可使用以下 PowerShell 命令来编辑用户的 adminDescription 属性：

`set-adusermyuser-replace@{adminDescription="PHSFiltered"}`

## <a name="excluded-users-is-larger-than-included-users"></a>排除的用户多于包含的用户

以下部分介绍当要 **排除** 的用户数 **多于** 要 **包含** 的用户数时，如何启用选择性密码哈希同步。

> [!IMPORTANT]
> 在继续操作之前，请确保禁用同步计划程序，如上文所述。

将在以下步骤中执行的操作汇总如下：

- 创建 In from AD – User AccountEnabled 的可编辑副本，取消选中启用密码哈希同步的选项，并定义其范围筛选器
- 创建默认 In from AD – User AccountEnabled 的另一个可编辑副本，选中启用密码哈希同步的选项，并定义其范围筛选器
- 重新启用同步计划程序
- 在 Active Directory 中设置特性值，该特性值定义为要在密码哈希同步中允许的用户的范围特性。

> [!IMPORTANT]
> 为配置选择性密码哈希同步而提供的步骤仅会影响 Active Directory 中使用值 PHSIncluded 填充属性 adminDescription 的用户对象 。
如果未填充此特性，或者值不是 **PHSIncluded**，则这些规则将不会应用于用户对象。

### <a name="configure-the-necessary-synchronization-rules"></a>配置必要的同步规则：

 1. 启动“同步规则编辑器”，并将筛选器“密码同步”设置为“开”，将“规则类型”设置为“标准”。
     ![规则类型](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. 为要将选择性密码哈希同步设置为开的 Active Directory 林选择规则 In from AD – User AccountEnabled，并单击“编辑”。 在下一个对话框中选择“是”，以创建原始规则的可编辑副本。
     ![In from AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. 第一个规则将禁用密码哈希同步。为新的自定义规则提供以下名称：**In from AD - User AccountEnabled - Filter Users from PHS**。
 将优先级值更改为小于 100 的数字（例如 **90** 或环境中可用的最小值）。
 请确保未选中“启用密码同步”和“已禁用”复选框。
 单击“下一步”。
  ![设置优先级](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. 在“范围筛选器”中，单击“添加子句”。
在特性列中选择“adminDescription”，在“运算符”列中选择“NOTEQUAL”，并输入 **PHSIncluded** 作为值。
     ![添加子句](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. 不需要进行其他更改。 “联接规则”和“转换”应保留默认的复制设置，因此你可以立即单击“保存”。
 在警告对话框中单击“确定”，该警告通知将在连接器的下一个同步周期运行完全同步。
     ![转换](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. 接下来，创建启用了密码哈希同步的另一个自定义规则。 为要将选择性密码哈希同步设置为开的 Active Directory 林再次选择默认规则 In from AD – User AccountEnabled，并单击“编辑”。 在下一个对话框中选择“是”，以创建原始规则的可编辑副本。
     ![User AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. 为新的自定义规则提供以下名称：**In from AD - User AccountEnabled - Users included for PHS**。
 将优先级值更改为一个小于之前创建的规则的数字（在此示例中为 89）。
 请确保选中复选框“启用密码同步”，并取消选中“禁用”复选框。
 单击“下一步”。
     ![启用密码同步](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. 在“范围筛选器”中，单击“添加子句”。
 在特性列中选择“adminDescription”，在“运算符”列中选择“EQUAL”，并输入 **PHSIncluded** 作为值。
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. 不需要进行其他更改。 “联接规则”和“转换”应保留默认的复制设置，因此你可以立即单击“保存”。
 在警告对话框中单击“确定”，该警告通知将在连接器的下一个同步周期运行完全同步。
     ![立即保存](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10. 确认创建规则。 删除筛选器“密码同步”中的“开”和“规则类型”中的“标准”。 你会看到刚创建的两个新规则。
     ![同步开](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>重新启用同步计划程序：

完成配置所需同步规则的步骤后，请使用以下步骤重新启用同步计划程序：

1. 在 Windows PowerShell 中，运行：

   `set-adsyncscheduler-synccycleenabled$true`

2. 然后运行以下内容，确认它已成功启用：

   `get-adsyncscheduler`

有关计划程序的详细信息，请参阅 [Azure AD Connect 同步计划程序](how-to-connect-sync-feature-scheduler.md)。

### <a name="edit-users-admindescription-attribute"></a>编辑用户 **adminDescription** 特性：

完成所有配置后，需要在 Active Directory 中为要在密码哈希同步中 **包含** 的所有用户编辑特性 **adminDescription**，并添加在范围筛选器中使用的字符串：**PHSIncluded**。

  ![编辑特性](media/how-to-connect-selective-password-hash-synchronization/include-11.png)

还可使用以下 PowerShell 命令来编辑用户的 adminDescription 属性：

`Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}`

## <a name="next-steps"></a>后续步骤

- [什么是密码哈希同步？](whatis-phs.md)
- [密码哈希同步的工作原理](how-to-connect-password-hash-synchronization.md)
