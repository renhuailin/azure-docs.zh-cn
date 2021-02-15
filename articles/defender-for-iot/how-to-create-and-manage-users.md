---
title: 创建和管理用户
description: 创建和管理传感器的用户和本地管理控制台。 可以为用户分配管理员、安全分析师或只读用户角色。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: fd0c7b74bea979737644824f93b4dce7a2364b99
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522336"
---
# <a name="about-defender-for-iot-console-users"></a>关于用于 IoT 控制台用户的 Defender

本文介绍如何创建和管理传感器的用户以及本地管理控制台。 用户角色包括管理员、安全分析师或只读用户。 每个角色都与针对传感器或本地管理控制台的工具的一系列权限相关联。 角色旨在促进对 Azure Defender IoT 的精确、安全的访问。

还可以使用功能来跟踪用户活动，并启用 Active Directory 登录。

默认情况下，会随 *cyberx 和支持* 用户一起安装每个传感器和本地管理控制台。 这些用户有权访问用于疑难解答和设置的高级工具。 管理员用户应使用这些用户凭据登录，创建管理员用户，然后为安全分析人员和只读用户创建额外的用户。

## <a name="role-based-permissions"></a>基于角色的权限
可用的用户角色如下：

- **只读**：只读用户在设备映射上执行查看警报和设备等任务。 这些用户可以访问 **导航** 下显示的选项。

- **安全分析师**：安全分析师具有只读用户权限。 他们还可以对设备执行操作、确认警报并使用调查工具。 这些用户可以访问 **导航** 和 **分析** 下显示的选项。

- **管理员**：管理员有权访问所有工具，包括定义系统配置、创建和管理用户等。 这些用户有权访问 " **导航**"、" **分析**" 和 " **管理**" 下显示的选项。

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>针对本地管理控制台工具的基于角色的权限

本部分介绍本地管理控制台的管理员、安全分析师和只读用户可用的权限。  

| 权限 | 只读 | 安全分析师 | 管理员 |
|--|--|--|--|
| 查看和筛选企业地图 | ✓ | ✓ | ✓ |
| 构建网站 |  |  | ✓ |
| 管理站点 (添加和编辑区域)  |  |  | ✓ |
| 查看和筛选设备清单 | ✓ | ✓ | ✓ |
| 查看和管理警报：确认、学习和固定 | ✓ | ✓ | ✓ |
| 生成报告 |  | ✓ | ✓ |
| 查看风险评估报表 |  | ✓ | ✓ |
| 设置警报排除项 |  | ✓ | ✓ |
| 查看或定义访问组 |  |  | ✓ |
| 管理系统设置 |  |  | ✓ |
| 管理用户 |  |  | ✓ |
| 向合作伙伴发送警报数据 |  |  | ✓ |
| 管理证书 |  |  | ✓ |
| 用户处于非活动状态时会话超时 | 30 分钟 | 30 分钟  | 30 分钟  |

#### <a name="assign-users-to-access-groups"></a>将用户分配到访问组

管理员可以通过将用户分配到特定 *访问组*，来增强 Defender for IoT 中的用户访问控制。 访问组将分配给传感器所在的区域、站点、区域和业务单位。 通过将用户分配到访问组，管理员可以对用户管理和分析设备检测的位置进行特定控制。 

采用这种方式时，可适应大型组织，其中的用户权限可能会很复杂或由全局组织安全策略确定。 有关详细信息，请参阅 [定义全局访问控制](how-to-define-global-user-access-control.md)。

### <a name="role-based-permissions-to-sensor-tools"></a>基于角色的传感器工具权限

本部分介绍可供传感器管理员、安全分析师和只读用户使用的权限。  

| 权限 | 只读 | 安全分析师 | 管理员 |
|--|--|--|--|
| 查看仪表板 | ✓ | ✓ | ✓ |
| 控件地图缩放视图 |  |  | ✓ |
| 查看警报 | ✓ | ✓ | ✓ |
| 管理警报：确认、学习和固定 |  | ✓ | ✓ |
| 查看时间线中的事件 |  | ✓ | ✓ |
| 授权设备，已知扫描设备，编程设备 |  | ✓ | ✓ |
| 查看调查数据 | ✓ | ✓ | ✓ |
| 管理系统设置 |  |  | ✓ |
| 管理用户 |  |  | ✓ |
| 用于反向查找的 DNS 服务器 |  |  | ✓ |
| 向合作伙伴发送警报数据 |  | ✓ | ✓ |
| 创建警报注释 |  | ✓ | ✓ |
| 查看编程更改历史记录 | ✓ | ✓ | ✓ |
| 创建自定义警报规则 |  | ✓ | ✓ |
| 同时管理多个通知 |  | ✓ | ✓ |
| 管理证书 |  |  | ✓ |
| 用户处于非活动状态时会话超时 | 30 分钟 | 30 分钟 | 30 分钟 |

## <a name="define-users"></a>定义用户

本部分介绍如何定义用户。 Cyberx、support 和 administrator 用户可以添加、删除和更新其他用户定义。

定义用户：

1. 在传感器或本地管理控制台的左窗格中，选择 " **用户**"。
1. 在 " **用户** " 窗口中，选择 " **创建用户**"。
1. 在 " **创建用户** " 窗格中，定义以下参数：

   - **用户名**：输入用户名。
   - **电子邮件**：输入用户的电子邮件地址。
   - **名字**：输入用户的名字。
   - **姓氏**：输入用户的姓氏。
   - **Role**：定义用户的角色。 请参阅 [基于角色的权限](#role-based-permissions)。
   - **访问组**：如果你要为本地管理控制台创建用户，请定义该用户的访问组。 请参阅 [定义全局访问控制](how-to-define-global-user-access-control.md)。
   - **密码**：选择用户类型，如下所示：
     - **本地用户**：定义传感器或本地管理控制台的用户的密码。 密码必须至少包含六个字符，且必须包含字母和数字。
     - **Active Directory 用户**：可以允许用户使用 Active Directory 凭据登录到传感器或管理控制台。 定义 Active Directory 组可以与特定权限级别关联。 例如，配置特定的 Active Directory 组，并将该组中的所有用户分配给只读用户类型。

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="管理用户。":::

## <a name="user-session-timeout"></a>用户会话超时

如果用户在特定时间未在键盘或鼠标上处于活动状态，则会从他们的会话中注销并重新登录。

如果用户在30分钟内未使用其控制台鼠标或键盘，则会强制执行会话注销。

此功能在默认情况下是启用的，但在升级时可以禁用。 此外，还可以更新会话计数时间。 会话时间以秒为单位定义。 定义按传感器和本地管理控制台应用。

如果已超过非活动超时时间，则会在控制台上显示会话超时消息。

### <a name="control-inactivity-sign-out"></a>控制非活动注销

管理员用户可以启用和禁用非活动注销，并调整不活动阈值。

访问命令：

1. 使用用于 IoT 的 IoT 管理凭据登录到传感器或本地管理控制台的 CLI。

1. 输入 `sudo nano /var/cyberx/properties/authentication`。

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

若要禁用该功能，请将更改 `infinity_session_expiration = true` 为 `infinity_session_expiration = false` 。

若要更新注销时间段，请将 `= <number>` 值调整为所需的时间。

## <a name="track-user-activity"></a>跟踪用户活动 

你可以在每个传感器的事件时间线中跟踪用户活动。 时间线显示事件或受影响的设备，以及用户执行活动的时间和日期。

查看用户活动：

1. 登录到传感器。
1. 在事件时间线中，启用 " **用户操作** " 选项。 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="查看用户的活动。":::

## <a name="integrate-with-active-directory-servers"></a>与 Active Directory 服务器集成 

将传感器或本地管理控制台配置为使用 Active Directory。 这允许 Active Directory 用户使用其 Active Directory 凭据访问 IoT 控制台的 Defender。

支持两种类型的基于 LDAP 的身份验证：

- **完全身份验证**：从 LDAP 服务器检索用户详细信息。 例如，名字、姓氏、电子邮件和用户权限。

- **可信用户**：仅检索用户密码。 检索到的其他用户详细信息基于传感器中定义的用户。

### <a name="active-directory-and-defender-for-iot-permissions"></a>用于 IoT 权限的 Active Directory 和 Defender

你可以将此处定义 Active Directory 组与特定权限级别关联。 例如，配置特定的 Active Directory 组，并为该组中的所有用户分配 "只读" 权限。 有关详细信息，请参阅 [创建和管理用户](how-to-create-and-manage-users.md) 。

配置 Active Directory：

1. 在左侧窗格中，选择 " **系统设置**"。

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="查看 Active Directory 系统设置。":::

1. 在 " **系统设置** " 窗格中，选择 **Active Directory**。

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="编辑您的 Active Directory 配置。":::

1. 在 "**编辑 Active Directory 配置**" 对话框中， **Active Directory 选择 "已启用集成**" "  >  **保存**"。 " **编辑 Active Directory 配置** " 对话框将展开，你现在可以输入用于配置 Active Directory 的参数。

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="输入用于配置 Active Directory 的参数。":::

    > [!NOTE]
    > - 必须严格按照 Active Directory 中显示的 LDAP 参数定义。
    > - 对于所有 Active Directory 参数，只使用小写。 即使 Active Directory 中的配置使用大写，也请使用小写。
    > - 不能同时为同一个域配置 LDAP 和 LDAPS。 不过，您可以同时对不同的域使用这两种方法。

1. 设置 Active Directory 服务器参数，如下所示：

   | 服务器参数 | 说明 |
   |--|--|
   | 域控制器 FQDN | 将完全限定的域名 (FQDN) 与 LDAP 服务器上显示的完全限定域名完全相同。 例如，输入 `host1.subdomain.domain.com`。 |
   | 域控制器端口 | 定义在其上配置 LDAP 的端口。 |
   | 主域 | 根据 LDAP 配置设置域名 (例如 `subdomain.domain.com`) 和连接类型。 |
   | Active Directory 组 | 输入在 LDAP 服务器上的 Active Directory 配置中定义的组名。 |
   | 受信任域 | 若要添加受信任的域，请添加域名称和受信任域的连接类型。 <br />只能为在 "用户" 下定义的用户配置受信任的域。 |

1. 选择“保存” 。

1. 若要添加受信任的服务器，请选择 " **添加服务器** " 并配置其他服务器。

## <a name="resetting-a-users-password-for-the-sensor-or-on-premises-management-console"></a>为传感器或本地管理控制台重置用户的密码

### <a name="cyberx-or-support-user"></a>CyberX 或支持用户

只有 **CyberX** 和 **支持** 用户才能访问 **密码恢复** 功能。 如果 **CyberX** 或 **支持** 用户忘记了其密码，则可以通过用于 IoT 登录页上的 **密码恢复** 选项来重置密码。

为 CyberX 或支持用户重置密码：

1. 在 "用于 IoT 的 Defender" 登录屏幕上，选择 "  **密码恢复**"。 " **密码恢复** " 屏幕打开。

1. 选择 " **CyberX** " 或 " **支持**"，并复制唯一标识符。

1. 导航到 Azure 门户，然后选择 " **站点和传感器**"。  

1. 选择顶部工具栏中的 " **订阅筛选器** " 图标 :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  ，并选择传感器连接到的订阅。

1. 选择 " **恢复本地管理控制台密码** " 选项卡。

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="选择 &quot;恢复本地管理&quot; 按钮以下载恢复文件。":::

1. 输入在 **密码恢复** 屏幕上收到的唯一标识符，然后选择 " **恢复**"。 `password_recovery.zip`下载文件。

    > [!NOTE]
    > 请勿更改密码恢复文件。 它是已签名的文件，如果你篡改此文件，则不会工作。

1. 在 **密码恢复** 屏幕上，选择 " **上传**"。 **"上传密码恢复文件"** 窗口将打开。

1. 选择 " **浏览** " 以查找 `password_recovery.zip` 文件，或者将拖动 `password_recovery.zip` 到窗口。

    > [!NOTE]
    > 可能会出现一条错误消息，指示文件无效。 若要修复此错误消息，请确保在下载之前选择了正确的订阅 `password_recovery.zip` ，并再次下载。  

1. 选择 " **下一步**"，随后将显示您的用户和您的管理控制台的系统生成的密码。

### <a name="administrator-security-analyst-and-read-only-user"></a>管理员、安全分析人员和只读用户

只读和安全分析人员无法重置其自己的密码，需要联系具有管理员、支持或 CyberX 角色的用户才能重置其密码。 管理员用户必须联系 **CyberX** 或 **支持** 用户才能重置其密码。

在传感器上重置用户的密码：

1. 管理员、支持或 CyberX 角色用户应登录到传感器。

1. 从左侧面板中选择 " **用户** "。

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="从左侧窗格中选择 &quot;用户&quot; 选项。":::

1. 找到用户，然后从 "**操作**" 下拉菜单中选择 "**编辑**"。

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="从 &quot;操作&quot; 下拉菜单中选择 &quot;编辑&quot;。":::

1. 在 " **新密码** " 和 " **确认新密码** " 字段中输入新密码。

1. 选择“更新”。

若要在本地管理控制台中重置用户的密码，请执行以下操作：

1. 管理员、支持或 CyberX 角色用户应登录到传感器。

1. 从左侧面板中选择 " **用户** "。

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="在左侧面板中，选择用户的选项。":::

1. 找到用户，然后选择 "编辑" 图标 :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: 。

1. 在 " **新密码** " 和 " **确认新密码** " 字段中输入新密码。

1. 选择“更新”。

## <a name="next-steps"></a>后续步骤

[激活并设置传感器](how-to-activate-and-set-up-your-sensor.md) 
[激活并设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md) 
[跟踪传感器活动](how-to-track-sensor-activity.md)
