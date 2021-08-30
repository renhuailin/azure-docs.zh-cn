---
title: 管理 Azure 门户设置和首选项
description: 更改 Azure 门户设置，例如默认订阅/目录、超时、菜单模式、对比度、主题、通知、语言/区域等。
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 285afaaf51f28b0fa53a9df2a9fa232b01d3fe24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741299"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>管理 Azure 门户设置和首选项

你可以根据自己的偏好更改 Azure 门户的默认设置。

可在“设置”菜单完成大部分设置，该菜单位于全局页面页眉的右上角。

:::image type="content" source="media/set-preferences/settings-top-header.png" alt-text="显示全局页面页眉中“设置”图标的屏幕截图。":::

> [!NOTE]
> 我们正在将所有用户移动到本主题中所述的最新的设置体验。 若要了解旧版体验，请参阅[管理 Azure 门户设置和首选项（旧版）](original-preferences.md)。

## <a name="directories--subscriptions"></a>目录 + 订阅

在“目录 + 订阅”页面中，可以管理目录和设置订阅筛选器。

### <a name="switch-and-manage-directories"></a>切换和管理目录

在“目录”部分中，你可看到自己的“当前目录”，即当前登录的目录 。

“启动目录”显示的是登录 Azure 门户时的默认目录。 若要选择其他启动目录，请选择“更改”以转至[外观 + 启动视图](#appearance--startup-views)页面，可在此页面中更改该选项。

若要查看自己有权访问的目录的完整列表，请选择“所有目录”。

若要收藏某个目录，请选择其星形图标。 “收藏夹”部分中会列出已收藏的目录。

若要切换到其他目录，请选择要处理的目录，然后选择它那一行的“切换”按钮。

:::image type="content" source="media/set-preferences/settings-directories-subscriptions-default-filter.png" alt-text="显示“目录设置”窗格的屏幕截图。":::

### <a name="subscription-filters"></a>订阅筛选器

可以选择在登录 Azure 门户时默认筛选的订阅。 如果你有所使用的主要的订阅列表，但偶尔也会使用其他订阅，此功能会很有用。

若要使用自定义筛选器，请选择“高级筛选器”。 在继续操作之前，系统会提示你确认。

:::image type="content" source="media/set-preferences/settings-advanced-filters-enable.png" alt-text="显示“高级筛选器”的确认对话框的屏幕截图。":::

这将启用“高级筛选器”页，可在其中创建和管理多个订阅筛选器。 任何当前选择的订阅都将保存为可再次使用的导入筛选器。 如果要停止使用高级筛选器，请再次选择切换开关以还原默认的订阅视图。 系统将保存你创建的所有自定义筛选器，以后启用“高级筛选器”时即可使用这些筛选器。

:::image type="content" source="media/set-preferences/settings-advanced-filters-disable.png" alt-text="显示禁用高级筛选器的确认对话框的屏幕截图。":::

## <a name="advanced-filters"></a>高级筛选器

在“高级筛选器”页上，可以创建、修改或删除订阅筛选器。

:::image type="content" source="media/set-preferences/settings-advanced-filters.png" alt-text="显示“高级筛选器”屏幕的屏幕截图。":::

“默认”筛选器会显示你有权访问的所有订阅。 如果没有其他筛选器，或者活动筛选器无法包含任何订阅，则使用此筛选器。

你还可以看到名为 Imported-filter 的筛选器，其中包含以前选择的所有订阅。

若要更改当前使用的筛选器，请从“高级筛选器”下拉框中选择该筛选器。 还可以选择“修改高级筛选器”以转到“高级筛选器”页，可在该页面中创建、修改和删除筛选器 。

### <a name="create-a-filter"></a>创建筛选器

若要创建新的筛选器，请选择“创建筛选器”。 最多可以创建十个筛选器。

每个筛选器必须具有长度为 8 到 50 个字符的唯一名称，并且该名称仅包含字母、数字和连字符。

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create.png" alt-text="显示“创建筛选器”选项的屏幕截图。":::

为筛选器命名后，请至少输入一个条件。 在“筛选器类型”字段中，选择“订阅名称”、“订阅 ID”或“订阅状态”   。 然后选择一个运算符并输入要筛选的值。

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create-operators.png" alt-text="显示适用于创建筛选器的运算符列表的屏幕截图。":::

添加完条件后，选择“创建”。 然后，该筛选器将显示在“活动筛选器”的列表中。

### <a name="modify-or-delete-a-filter"></a>修改或删除筛选器

选择现有筛选器那一行中的铅笔图标，可以对其进行修改或重命名。 进行所需的更改，然后选择“应用”。

> [!NOTE]
> 如果修改当前处于活动状态的筛选器，并且更改后的筛选结果为 0 个订阅，则“默认”筛选器将变为活动状态。 无法激活不包含任何订阅的筛选器。

若要删除筛选器，请选择该筛选器那一行中的垃圾桶图标。 无法删除“默认”筛选器或任何当前处于活动状态的筛选器。

## <a name="appearance--startup-views"></a>外观 + 启动视图

“外观 + 启动视图”窗格有两个部分。 在“外观”部分中，可以选择菜单行为、颜色主题以及是否使用高对比度主题；在“启动视图”部分中，可以对与首次登录到 Azure 门户时看到的内容相关的选项进行设置 。

:::image type="content" source="media/set-preferences/azure-portal-settings-appearance.png" alt-text="显示“外观 + 启动视图”的“外观”部分的屏幕截图。":::

### <a name="set-menu-behavior"></a>设置菜单行为

在“菜单行为”部分中，可以选择默认 Azure 门户菜单的行为方式。

- **浮出**：菜单将在不需要时隐藏。 可以选择左上角的菜单图标来打开或关闭菜单。
- **停靠**：菜单将始终可见。 可以折叠菜单以提供更多的工作空间。

### <a name="choose-a-theme-or-enable-high-contrast"></a>选择主题或启用高对比度

选择的主题将影响 Azure 门户中显示的背景和字体颜色。 在“主题”部分中，可以从四种预设的颜色主题中进行选择。 选择每个缩略图找到最适合自己的主题。

或者，可以在“高对比度主题”部分中选择主题。 这些主题可使 Azure 门户中的内容更易于阅读（尤其是对于有视觉障碍的人）。 选择白色或黑色高对比度主题将覆盖所有其他主题选择。

### <a name="startup-page"></a>启动页

为首次登录 Azure 门户时看到的页面选择以下选项之一。

- **主页**：显示主页，包含常用 Azure 服务的快捷方式、最近使用的资源列表以及指向工具、文档等内容的有用链接。
- **仪表板**：显示最近使用的仪表板。 可以自定义仪表板，以创建专为你设计的工作区。 例如，可以生成一个以项目、任务或角色为中心的仪表板。 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](azure-portal-dashboards.md)。

### <a name="startup-directory"></a>启动目录

为首次登录 Azure 门户时处理的目录选择以下选项之一。

- **登录到上次访问的目录**：在登录到 Azure 门户时，你将从上次处理的目录开始操作。
- **选择目录**：选择此选项以选择一个目录。 每次登录至 Azure 门户时，你都将从该目录开始操作，即使上次处理的是其他目录也是一样。

:::image type="content" source="media/set-preferences/azure-portal-settings-startup-views.png" alt-text="显示“外观 + 启动视图”的“启动”部分的屏幕截图。":::

## <a name="language--region"></a>语言 + 区域

选择语言和区域格式，这将影响日期和货币等数据在 Azure 门户的显示方式。

:::image type="content" source="media/set-preferences/azure-portal-settings-language-region.png" alt-text="显示“语言 + 区域”设置窗格的屏幕截图。":::

> [!NOTE]
> 这些语言和区域设置只影响 Azure 门户。 在新选项卡或窗口中打开的文档链接会使用浏览器的设置来确定要显示的语言。

### <a name="language"></a>语言

使用下拉列表从可用语言列表中进行选择。 该设置控制的是文本在 Azure 门户中的显示语言。

### <a name="regional-format"></a>区域格式

选择一个选项以控制日期、时间、数字和货币在 Azure 门户中的显示方式。

“区域格式”下拉列表中显示的选项会根据为“语言”选择的选项而发生变化 。 例如，如果将语言选择为“英语”，然后将区域格式选择为“英语(美国)”，则货币会显示为美元 。 如果将语言选择为“英语”，然后将区域格式选择为“英语(欧洲)”，则货币会显示为欧元 。

选择“应用”以更新语言和区域格式设置。

## <a name="my-information"></a>我的信息

在“我的信息”页面中，可以更新用于接收关于 Azure 服务、账单、支持或安全问题的更新的电子邮件地址。 还可以选择接收或停止接收关于 Microsoft Azure 和其他产品及服务的其他电子邮件。

在“我的信息”页面顶部附近，可以看到用于导出、还原或删除设置的选项。

:::image type="content" source="media/set-preferences/settings-my-information.png" alt-text="“我的信息”设置页的屏幕截图。":::

### <a name="export-user-settings"></a>导出用户设置

有关自定义设置的信息存储在 Azure 中。 可以导出以下用户数据：

- Azure 门户中的专用仪表板
- 用户设置，例如最喜欢的订阅或目录
- 主题和其他自定义门户设置

如果计划删除设置，建议先导出并检查这些设置。 重新生成仪表板或重新进行设置可能会非常耗时。

若要导出门户设置，请选择设置“概述”窗格顶部的“导出设置” 。 该操作会创建包含用户设置数据的 .json 文件。

由于用户设置的动态性质和数据损坏风险，因此不能从 .json 文件导入设置。

### <a name="restore-default-settings"></a>还原默认设置

如果已更改 Azure 门户设置，并且想放弃这些设置，请从设置“概述”窗格顶部选择“还原默认设置” 。 你将收到确认此操作的提示。 确认还原默认设置后，对 Azure 门户设置进行的所有更改都将丢失。 此选项不影响仪表板自定义。

### <a name="delete-user-settings-and-dashboards"></a>删除用户设置和仪表板

有关自定义设置的信息存储在 Azure 中。 可以删除以下用户数据：

- Azure 门户中的专用仪表板
- 用户设置，例如最喜欢的订阅或目录
- 主题和其他自定义门户设置

在删除设置之前，建议先导出并检查设置。 重新生成[仪表板](azure-portal-dashboards.md)或重做自定义设置可能非常耗时。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

若要删除门户设置，请选择“我的信息”页面顶部的“删除所有设置和专用仪表板” 。 系统会提示你确认该删除操作。 确认删除操作后，所有设置自定义项都将返回到默认设置，并且所有专用仪表板都将丢失。

## <a name="signing-out--notifications"></a>注销 + 通知

通过此窗格可以管理弹出通知和会话超时。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-notifications.png" alt-text="显示“注销 + 通知”窗格的屏幕截图。":::

### <a name="signing-out"></a>注销

如果你忘记了保护工作站，非活动状态超时设置有助于保护资源免受未经授权的访问。 空闲一段时间后，将自动注销你的 Azure 门户会话。 作为个人，你可以更改自己的超时设置。 如果你是管理员，可在目录级别为目录中所有用户完成这项设置。

### <a name="change-your-individual-timeout-setting-user"></a>更改个人的超时设置（用户）

在“不活动时将我注销”旁的下拉菜单中，选择在保持空闲状态多长时间之后将你的 Azure 会话注销。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive.png" alt-text="显示用户超时设置选项的屏幕截图。":::

选择“应用”以保存所做的更改。 之后，如果在门户会话期间处于非活动状态，Azure 门户将在设置的持续时间后注销。

如果你的管理员已经启用了非活动状态超时策略，你仍然可以设置自己的超时设置，只是该时间必须短于目录级别设置。 为此，请选择“替代目录非活动超时策略”策略，然后为“替代值”输入时间间隔 。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-user.png" alt-text="显示目录非活动超时替代设置的屏幕截图。":::

### <a name="change-the-directory-timeout-setting-admin"></a>更改目录超时设置（管理员）

具有[全局管理员角色](../active-directory/roles/permissions-reference.md#global-administrator)的管理员可以强制实施注销会话前的最大空闲时间。此非活动超时设置应用于目录级别。 设置对新会话生效。 它不会立即应用于任何已登录的用户。 有关目录的详细信息，请参阅 [Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

如果你是全局管理员，并且想要对 Azure 门户的所有用户强制实施空闲超时设置，请选择“启用目录级别空闲超时”以启用该设置。 然后输入小时数和分钟数，以表示用户在会话自动注销之前可以处于非活动状态的最大时间。选择“应用”后，此设置将应用于目录中的所有用户  。

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-admin.png" alt-text="显示目录级别空闲超时选项的屏幕截图。":::

若要确认已正确设置非活动超时策略，请从全局页面页眉中选择“通知”，并检查其中是否列出了成功通知。

:::image type="content" source="media/set-preferences/confirmation.png" alt-text="显示表示成功设置非活动超时策略的通知的屏幕截图。":::

### <a name="enable-or-disable-pop-up-notifications"></a>启用或禁用弹出式通知

通知是与当前会话相关的系统消息。 这些消息能提供一些信息，例如显示当前额度余额、确认上一个操作或让你知道创建的资源何时生效。 启用弹出式通知后，消息会短暂地显示在屏幕的右上角。

若要启用或禁用弹出式通知，请选中或清除“启用弹出式通知”。

若要阅读在当前会话中收到的所有通知，请从全局页头中选择“通知”。

:::image type="content" source="media/set-preferences/read-notifications.png" alt-text="显示全局页眉中“通知”图标的屏幕截图。":::

若要查看以前会话中的通知，请查看活动日志中的事件。 有关详细信息，请参阅[查看活动日志](../azure-monitor/essentials/activity-log.md#view-the-activity-log)。

## <a name="next-steps"></a>后续步骤

- [了解 Azure 门户中的键盘快捷方式](azure-portal-keyboard-shortcuts.md)
- [查看支持的浏览器和设备](azure-portal-supported-browsers-devices.md)
- [添加、删除和重新排列收藏夹](azure-portal-add-remove-sort-favorites.md)
- [创建并共享自定义仪表板](azure-portal-dashboards.md)
- [观看 Azure 门户的操作说明视频](azure-portal-video-series.md)
