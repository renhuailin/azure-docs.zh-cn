---
title: 创建和管理用户
description: 创建和管理传感器和本地管理控制台的用户。 可以为用户分配管理员、安全分析师或只读用户角色。
ms.date: 05/20/2021
ms.topic: article
ms.openlocfilehash: fa10961218d536faf09a1f7062c8a8c77dc99ce4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434027"
---
# <a name="about-defender-for-iot-console-users"></a>关于 Defender for IoT 控制台用户

本文介绍如何创建和管理传感器和本地管理控制台的用户。 用户角色包括管理员、安全分析师或只读用户。 每个角色都与传感器或本地管理控制台工具的一系列权限相关联。 角色旨在促进对 Azure Defender for IoT 的粒度安全访问。

还提供了跟踪用户活动和启用 Active Directory 登录的功能。

默认情况下，每个传感器和本地管理控制台都安装有 cyberx 和 support 用户。 这些用户可以使用高级工具进行故障排除和设置。 管理员用户应该使用这些用户凭据登录，创建管理员用户，然后为安全分析师和只读用户创建额外的用户。

## <a name="role-based-permissions"></a>基于角色的权限
以下用户角色可用：

- **只读**：只读用户执行任务，如查看设备映射上的警报和设备。 这些用户可以访问“导航”下显示的选项。

- **安全分析师**：安全分析师拥有只读用户权限。 他们还可以在设备上执行操作、确认警报和使用调查工具。 这些用户可以访问“导航”和“分析”下显示的选项 。

- **管理员**：管理员可以访问所有工具，包括定义系统配置、创建和管理用户等等。 这些用户可以访问“导航”、“分析”和“管理”下显示的选项  。

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>对本地管理控制台工具的基于角色的权限

本部分介绍管理员、安全分析师和只读用户对本地管理控制台可用的权限。  

| 权限 | 只读 | 安全分析师 | 管理员 |
|--|--|--|--|
| 查看和筛选企业映射 | ✓ | ✓ | ✓ |
| 构建站点 |  |  | ✓ |
| 管理站点（添加和编辑区域） |  |  | ✓ |
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
| 用户处于非活动时的会话超时 | 30 分钟 | 30 分钟  | 30 分钟  |

#### <a name="assign-users-to-access-groups"></a>将用户分配到访问组

管理员可以通过将用户分配到特定的访问组来增强 IoT Defender 中的用户访问控制。 将访问组分配给传感器所在的区域、站点、地区和业务单位。 通过将用户分配到访问组，管理员可以具体控制用户在何处管理和分析设备检测。 

这种方式适用于用户权限复杂或由全局组织安全策略确定的大型组织。 有关详细信息，请参阅[定义全局访问控制](how-to-define-global-user-access-control.md)。

### <a name="role-based-permissions-to-sensor-tools"></a>对传感器工具的基于角色的权限

本部分介绍传感器管理员、安全分析师和只读用户可用的权限。  

| 权限 | 只读 | 安全分析师 | 管理员 |
|--|--|--|--|
| 查看仪表板 | ✓ | ✓ | ✓ |
| 控件映射缩放视图 |  |  | ✓ |
| 查看警报 | ✓ | ✓ | ✓ |
| 管理警报：确认、学习和固定 |  | ✓ | ✓ |
| 查看时间线中的事件 |  | ✓ | ✓ |
| 授权设备、已知扫描设备、编程设备 |  | ✓ | ✓ |
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
| 用户处于非活动时的会话超时 | 30 分钟 | 30 分钟 | 30 分钟 |

## <a name="define-users"></a>定义用户

本部分介绍如何定义用户。 Cyberx、support 和 administrator 用户可以添加、删除和更新其他用户定义。

若要定义用户，请执行以下操作：

1. 从传感器或本地管理控制台的左侧窗格中，选择“用户”。
1. 在“用户”窗口中，选择“创建用户” 。
1. 在“创建用户”窗格中，定义以下参数：

   - **用户名**：输入用户名。
   - **电子邮件**：输入用户的电子邮件地址。
   - **名字**：输入用户的名字。
   - **姓氏**：输入用户的姓氏。
   - **角色**：定义用户的角色。 请参阅[基于角色的权限](#role-based-permissions)。
   - **访问组**：如果要为本地管理控制台创建用户，则定义用户的访问组。 请参阅[定义全局访问控制](how-to-define-global-user-access-control.md)。
   - **密码**：选择用户类型，如下所示：
     - **本地用户**：为传感器或本地管理控制台的用户定义密码。 密码必须至少包含六个字符，并且必须包含字母和数字。
     - **Active Directory 用户**：可以允许用户使用 Active Directory 凭据登录传感器或管理控制台。 定义的 Active Directory 组可以与特定权限级别相关联。 例如，配置特定的 Active Directory 组并将组中的所有用户分配给只读用户类型。

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="管理用户。":::

## <a name="user-session-timeout"></a>用户会话超时

如果用户在特定时间内没有使用键盘或鼠标，他们将退出会话，必须重新登录。

如果用户已在 30 分钟内没有使用控制台鼠标或键盘，系统会强制注销会话。

此功能在默认情况下和升级时启用，但可以禁用。 此外，还可以更新会话计数时间。 会话时间以秒为单位定义。 该定义适用于传感器和本地管理控制台。

如果发生非活动超时，控制台将显示会话超时消息。

### <a name="control-inactivity-sign-out"></a>控制非活动注销

管理员用户可以启用和禁用非活动注销并调整非活动阈值。

若要访问命令，请执行以下操作：

1. 使用 Defender for IoT 管理凭据登录到传感器或本地管理控制台的 CLI。

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

若要禁用该功能，请将 `infinity_session_expiration = true` 更改为 `infinity_session_expiration = false`。

若要更新注销计数周期，请将 `= <number>` 值调整为所需时间。

## <a name="track-user-activity"></a>跟踪用户活动

你可以在每个传感器的事件时间线中跟踪用户活动。 时间线显示事件或受影响的设备，以及用户执行活动的时间和日期。

若要查看用户活动，请执行以下操作：

1. 登录传感器。

1. 在事件时间线中，启用“用户操作”选项。

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="查看用户活动。":::

## <a name="integrate-with-active-directory-servers"></a>与 Azure Active Directory 服务器集成

将传感器或本地管理控制台配置为使用 Active Directory。 这允许 Active Directory 用户使用其 Active Directory 凭据访问 Defender for IoT 控制台。

> [!Note]
> LDAP v3 受支持。

支持两种基于 LDAP 的身份验证：

- **完全身份验证**：从 LDAP 服务器检索用户详细信息。 例如名字、姓氏、电子邮件和用户权限。

- **受信任的用户**：仅检索用户密码。 检索到的其他用户详细信息基于传感器中定义的用户。

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory 和 Defender for IoT 权限

你可以将此处定义的 Active Directory 组与特定权限级别关联。 例如，配置特定的 Active Directory 组并将只读权限分配给组中的所有用户。

若要配置 Active Directory，请执行以下操作：

1. 在左窗格中，选择“系统设置”。

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="查看 Active Directory 系统设置。":::

1. 在“系统设置”窗格中，选择 Active Directory 。

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="编辑 Active Directory 配置。":::

1. 在“编辑 Active Directory 配置”对话框中，选择“已启用 Active Directory 集成” > “保存”  。 将展开“编辑 Active Directory 配置”对话框，现在可以输入参数来配置 Active Directory。

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="输入参数以配置 Active Directory。":::

> [!NOTE]
> - 必须在此处定义与 Active Directory 中完全相同的 LDAP 参数。
> - 对于所有 Active Directory 参数，请仅使用小写字母。 即使 Active Directory 中的配置使用大写，也请使用小写。
> - 不能为同一域同时配置 LDAP 和 LDAP。 但是，你可以同时对不同的域使用这两种方法。

1. 设置 Active Directory 服务器参数，如下所示：

   | 服务器参数 | 说明 |
   |--|--|
   | 域控制器 FQDN | 将完全限定域名 (FQDN) 设置为与 LDAP 服务器上显示的完全一致。 例如，输入 `host1.subdomain.domain.com`。 |
   | 域控制器端口 | 定义配置 LDAP 的端口。 |
   | 主域 | 根据 LDAP 配置设置域名（例如，`subdomain.domain.com`）和连接类型。 |
   | Active Directory 组 | 输入在 LDAP 服务器上的 Active Directory 配置中定义的组名。 |
   | 受信任的域 | 若要添加受信任的域，请添加受信任的域的域名和连接类型。 <br />只能为在“用户”下定义的用户配置受信任的域。 |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>本地管理控制台的 ActiveDirectory 组

如果要为本地管理控制台用户创建 Active Directory 组，则必须为每个 Active Directory 组创建访问组规则。 如果 Active Directory 用户组不存在访问组规则，本地管理控制台 Active Directory 凭据将不起作用。 请参阅[定义全局访问控制](how-to-define-global-user-access-control.md)。

1. 选择“保存”。

1. 若要添加受信任的服务器，请选择“添加服务器”，然后配置另一个服务器。

## <a name="change-a-users-password"></a>更改用户的密码

管理员可以更改安全分析师和只读角色的密码。 管理员角色用户不能更改自己的密码，必须联系更高级别的角色。 安全分析师和只读角色无法重置其自己的或任何其他角色的密码。 安全分析师和只读角色需要联系角色级别更高的用户来重置其密码。 CyberX 角色可以更改所有用户角色的密码。 支持角色可以更改支持人员、管理员、安全分析师和只读用户角色的密码。  

若要在传感器上重置用户的密码，请执行以下操作：

1. 使用一个具有管理员角色、支持角色或 CyberX 角色的用户登录到传感器。

1. 从左侧面板中选择“用户”。

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="从左侧窗格中选择“用户”选项。":::

1. 找到用户并从“操作”下拉菜单中选择“编辑” 。

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="从“操作”下拉菜单中选择“编辑”。":::

1. 在“新密码”和“确认新密码”字段中输入新密码 。

    > [!NOTE]
    > 密码必须至少为 16 个字符，必须包含小写和大写字母字符、数字和以下符号之一：#%*+,-./:=?@[]^_{}~

1. 选择“更新”。

若要在本地管理控制台上重置用户的密码，请执行以下操作：

1. 使用一个具有管理员角色、支持角色或 CyberX 角色的用户登录到本地管理控制台。

1. 从左侧面板中选择“用户”。

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="在左侧面板上选择用户选项。":::

1. 找到用户，然后选择编辑图标:::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false":::。

1. 在“新密码”和“确认新密码”字段中输入新密码 。

    > [!NOTE]
    > 密码必须至少为 16 个字符，必须包含小写和大写字母字符、数字和以下符号之一：#%*+,-./:=?@[]^_{}~

1. 选择“更新”。

## <a name="recover-the-password-for-the-on-premises-management-console-or-the-sensor"></a>为本地管理控制台或传感器恢复密码

可以使用“密码恢复”功能为本地管理控制台或传感器恢复密码。 只有 CyberX 和 Support 用户才能访问“密码恢复”功能。

若要为本地管理控制台或传感器恢复密码，请执行以下操作：

1. 在本地管理控制台或传感器的登录屏幕上，选择“密码恢复”。 此时会打开“密码恢复”屏幕。

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery.png" alt-text="从本地管理控制台或传感器的登录屏幕中选择“密码恢复”。":::

1. 从下拉菜单中选择“CyberX”或“Support”，并复制唯一标识符代码 。

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery-screen.png" alt-text="从下拉菜单中选择 CyberX 用户或 Support 用户。":::

1. 导航到 Azure 门户，然后选择“站点和传感器”。  

1. 从顶部工具栏中选择“订阅筛选器”图标:::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::，然后选择传感器连接到的订阅。

1. 选择“更多操作”下拉菜单，然后选择“恢复本地管理控制台密码”。

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text="选择传感器，然后选择“恢复本地管理控制台密码”选项。":::   

1. 输入在“密码恢复”屏幕上收到的唯一标识符，然后选择“恢复” 。 此时将下载 `password_recovery.zip` 文件。

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="输入唯一标识符，然后选择“恢复”":::。

    > [!NOTE]
    > 请勿更改密码恢复文件。 这是一个已签名的文件，被篡改后会失效。

1. 在“密码恢复”屏幕上，选择“上传”。 此时将打开“上传密码恢复文件”窗口。

1. 选择“浏览”以找到 `password_recovery.zip` 文件，或将 `password_recovery.zip` 拖到窗口。

    > [!NOTE]
    > 可能会出现一条错误消息，指示文件无效。 若要修复此错误消息，请确保在下载 `password_recovery.zip` 之前选择了正确的订阅，然后再次下载。  

1. 选择“下一步”，此时将出现你的用户和系统生成的管理控制台密码。

## <a name="next-steps"></a>后续步骤

[激活并设置传感器](how-to-activate-and-set-up-your-sensor.md)
[激活并设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)
[跟踪传感器活动](how-to-track-sensor-activity.md)
