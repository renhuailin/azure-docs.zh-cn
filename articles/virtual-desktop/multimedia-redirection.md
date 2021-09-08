---
title: Azure 虚拟桌面上的多媒体重定向 - Azure
description: 如何使用 Azure 虚拟桌面的多媒体重定向（预览版）。
author: Heidilohr
ms.topic: how-to
ms.date: 08/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d479dbc34bd8c08ebc471de74be1558f6dccc6e1
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273242"
---
# <a name="multimedia-redirection-for-azure-virtual-desktop-preview"></a>Azure 虚拟桌面的多媒体重定向（预览版）

> [!IMPORTANT]
> Azure 虚拟桌面的多媒体重定向目前处于预览阶段。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

>[!NOTE]
>对于 Microsoft 365 Government (GCC)、GCC-High 环境和 Microsoft 365 DoD，Azure 虚拟桌面目前不支持 Azure 虚拟桌面上的多媒体重定向。
>
>Azure 虚拟桌面上的多媒体重定向仅适用于 Windows 10 计算机上的 Windows 桌面客户端。 多媒体重定向需要安装 Windows 桌面客户端版本 1.2.2222 或更高版本。

借助多媒体重定向 (MMR)，可在 Azure 虚拟桌面浏览器中观看视频时流畅地播放视频。 多媒体重定向可将媒体元素从浏览器远程传输到本地计算机，以加快处理和呈现速度。 Microsoft Edge 和 Google Chrome 都支持多媒体重定向功能。 但是，Azure 虚拟桌面多媒体重定向的公共预览版在 YouTube 上的播放受到限制。 若要在组织的部署中测试 YouTube，你需要[启用扩展](#managing-group-policies-for-the-multimedia-redirection-browser-extension)。

## <a name="requirements"></a>要求

在使用 Azure 虚拟桌面上的多媒体重定向之前，需要执行以下操作：

1. 在 Windows 10 或 Windows 10 IoT 企业设备上[安装 Windows 桌面客户端](./user-documentation/connect-windows-7-10.md#install-the-windows-desktop-client)，设备需满足 [Windows 电脑上 Teams 的硬件要求](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)。 安装 1.2.2222 或更高版本的客户端时，还将在客户端设备上安装多媒体重定向插件 (MsMmrDVCPlugin.dll)。 若要详细了解更新和新版本，请参阅 [Windows 桌面客户端中的新功能](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)。

2. [为用户创建主机池](create-host-pools-azure-marketplace.md)。

3. 配置客户端计算机，以允许用户访问预览体验计划。 若要针对预览体验组来配置客户端，请设置以下注册表信息：

   - **键**：HKLM\\Software\\Microsoft\\MSRDC\\Policies
   - **类型**：REG_SZ
   - **名称**：ReleaseRing
   - **数据**：insider

   若要详细了解预览体验计划，请参阅[适用于管理员的 Windows 桌面客户端](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin#configure-user-groups)。

4. 使用 [MSI 安装程序 (MsMmrHostMri)](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) 在 Azure VM 上为 Internet 浏览器安装多媒体重定向扩展。 Azure 虚拟桌面的多媒体重定向目前仅支持 Microsoft Edge 和 Google Chrome。

## <a name="managing-group-policies-for-the-multimedia-redirection-browser-extension"></a>管理多媒体重定向浏览器扩展的组策略

使用多媒体重定向 MSI 将安装浏览器扩展。 但是，由于此服务仍处于公共预览阶段，因此，用户体验可能会有所不同。 有关已知问题的详细信息，请参阅[已知问题](#known-issues-and-limitations)。

在某些情况下，可以更改组策略来管理浏览器扩展并改善用户体验。 例如：

- 可以在没有用户交互的情况下安装扩展。
- 可以限制哪些网站使用多媒体重定向。
- 默认情况下，可以在 Google Chrome 中固定扩展图标。 默认情况下，Microsoft Edge 中已固定扩展图标，因此只需在 Chrome 中更改此设置。

### <a name="configure-microsoft-edge-group-policies-for-multimedia-redirection"></a>为多媒体重定向配置 Microsoft Edge 组策略

若要配置组策略，你需要编辑 Microsoft Edge 管理模板。 你应该会在“管理模板 Microsoft Edge 扩展” > “配置扩展管理设置”下看到扩展配置选项。

以下代码是 Microsoft Edge 组策略的一个示例，它使浏览器安装多媒体重定向扩展并仅允许在 YouTube 上加载多媒体重定向：

```cmd
{ "joeclbldhdmoijbaagobkhlpfjglcihd": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "update_url": "https://edge.microsoft.com/extensionwebstorebase/v1/crx" } }
```

若要详细了解组策略配置，请参阅 [Microsoft Edge 组策略](/DeployEdge/configure-microsoft-edge)。

### <a name="configure-google-chrome-group-policies-for-multimedia-redirection"></a>为多媒体重定向配置 Google Chrome 组策略

若要配置 Google Chrome 组策略，你需要编辑 Google Chrome 管理模板。 你应该会在“管理模板” > “Google” > “Google Chrome 扩展” > ”扩展管理设置“下看到扩展配置选项。

以下示例与[为多媒体重定向配置 Microsoft Edge 组策略](#configure-microsoft-edge-group-policies-for-multimedia-redirection)中的代码示例非常相似。 此策略将强制通过右上方菜单中固定的图标安装多媒体重定向扩展，并且只允许在 YouTube 上加载多媒体重定向。

```cmd
{ "lfmemoeeciijgkjkgbgikoonlkabmlno": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "toolbar_pin": "force_pinned", "update_url": "https://clients2.google.com/service/update2/crx" } }
```

有关配置 [Google Chrome 组策略](https://support.google.com/chrome/a/answer/187202#zippy=%2Cwindows)的其他信息。

## <a name="run-the-multimedia-redirection-extension-manually-on-a-browser"></a>在浏览器上手动运行多媒体重定向扩展

MMR 使用远程应用以及 Microsoft Edge 和 Google Chrome 浏览器的会话桌面。 满足[要求](#requirements)后，打开受支持的浏览器。 如果你没有使用组策略安装浏览器或扩展，用户将需要手动运行扩展。 本部分将介绍如何在当前支持的浏览器之一中手动运行扩展。

### <a name="microsoft-edge"></a>Microsoft Edge

若要在 Microsoft Edge 上手动运行扩展，请在溢出菜单上查找黄色感叹号。 你应该会看到关于启用 Azure 虚拟桌面多媒体重定向扩展的提示。 选择“启用扩展”。

### <a name="google-chrome"></a>Google Chrome

若要在 Google Chrome 上手动运行扩展，请查找显示已安装新扩展的通知消息，如下面的屏幕截图所示。 

![Google Chrome 任务栏的屏幕截图。 其中有一个通知选项卡，显示“添加了新扩展”。](media/chrome-notification.png)

选择该通知，以允许用户启用扩展。 用户还应该固定扩展，以便可以通过图标了解是否连接了多媒体重定向。

### <a name="the-multimedia-redirection-status-icon"></a>多媒体重定向状态图标

为了快速判断浏览器中的多媒体重定向是否处于活动状态，我们添加了以下图标状态：

| 图标状态  | 定义  |
|-----------------|-----------------|
| ![未应用任何状态的默认 Azure 虚拟桌面程序图标。](./media/icon-default.png) | 未应用任何状态的默认图标外观。 |
| ![Azure 虚拟桌面程序图标有一个带 x 的红色方块，表示多媒体重定向不起作用。](./media/icon-disconnect.png) | 带“X”的红色方块表示客户端无法连接到多媒体重定向。 |
| ![Azure 虚拟桌面程序图标有一个带对勾的绿色方块，表示多媒体重定向正在运行。](./media/icon-connect.png) | 带对勾的绿色方块表示客户端已成功连接到多媒体重定向。 |

选择该图标将显示一个弹出菜单，其中包含一个复选框，你可以选择在所有网站上启用或禁用多媒体重定向。 它还列出了服务的每个组件的版本号。

## <a name="support-during-public-preview"></a>公开预览期间的支持
Microsoft 支持在公共预览期间不处理多媒体重定向问题。

如果遇到任何问题，可以在客户端和 VM 主机上的反馈中心告诉我们。

若要向我们发送反馈，请执行以下操作：

1. 在客户端和服务器上打开“反馈中心”。

2. 选择“报告问题”。

3. 对这两个问题报告使用相同的标题，但通过在开头添加“[客户端]”或“[主机]”来指定提交报告的位置。

    例如，如果从客户端提交问题，可以将其格式化为：

    >[客户端] 报告标题

    如果从主机提交问题，可以将其格式化为：

    >[主机] 报告标题

4. 在“详细说明”字段中，描述你遇到的问题。 建议同时提供出现问题时你正在观看的视频的 URL。

5. 完成后，选择“下一步”。

6. 选择“问题”气泡，然后从两个下拉菜单中选择“应用”和“远程桌面”，如下面的屏幕截图所示。

    ![“2. 选择类别”窗口的屏幕截图。 用户选择了“问题”气泡，然后在其下方的下拉菜单中选择了“应用”和“远程桌面”。](media/problem-category.png)

7. 选择“下一步”。

8. 查看列表中是否存在与你计划提交的问题类似的问题。
   
   - 如果出现链接到活动 bug 的气泡，请确保 bug 的描述与你要报告的问题相符。 如果相符，请选择该气泡，然后选择“链接到 bug”，如下面的屏幕截图所示。

    ![“3. 查找类似反馈”窗口的屏幕截图。 用户选择了“链接到活动 bug 编号 32350300”选项的气泡。](media/link-to-bug.png)

    - 如果你没有看到类似的问题，请选择“新建 bug”。

    ![“3. 查找类似反馈”窗口的屏幕截图。 这一次，“链接到 bug”选项不存在，用户改为选择“新建 bug”。](media/make-new-bug.png)

9. 选择“下一步”。

10. 在“添加更多详细信息”窗口中，选择“包括有关远程桌面的数据(默认)”，然后尽可能详细地回答所有问题。

    如果你想添加问题的视频录制，请选择“包括有关远程桌面的数据(默认)”，然后选择“开始录制”按钮。 录制时，打开远程桌面并执行导致问题发生的过程。 完成后，返回到浏览器，然后测试视频以确保其录制正确。

    完成后，同意将附加文件和诊断发送给 Microsoft，然后选择“提交”。

### <a name="known-issues-and-limitations"></a>已知问题和限制

下面是我们已经知道的问题，因此你无需报告：

- 多媒体重定向仅适用于 Windows 桌面客户端，不适用于 Web 客户端。

- 多媒体重定向目前不支持受保护的内容，因此来自 Pluralsight 和 Netflix 的视频无法播放。

- 在公共预览期间，除 YouTube 之外的所有网站都将禁用多媒体重定向。 但是，如果你有扩展，则可以为所有网站启用多媒体重定向。 我们添加了扩展，以便组织可以在公司网站上测试该功能。

- MSI 安装程序在内部测试期间无法安装扩展的可能性很小。 如果遇到此问题，则需要从 Microsoft Edge Store 或 Google Chrome Store 安装多媒体重定向扩展。

    - [多媒体重定向浏览器扩展 (Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
    - [多媒体浏览器扩展 (Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- 使用 MSI 安装程序在主机上安装扩展时，系统将在用户第一次打开浏览器时提示其接受扩展，或者显示警告或错误消息。 如果用户拒绝此提示，可能会导致无法加载扩展。 为避免此问题，请通过[编辑组策略](#managing-group-policies-for-the-multimedia-redirection-browser-extension)来安装扩展。

- 当你重设视频窗口的大小时，窗口的大小将比视频本身调整得更快。 最小化和最大化窗口时，也会看到此问题。

## <a name="next-steps"></a>后续步骤

如果你对 Azure 虚拟桌面其他部分的视频流式处理感兴趣，请查看[适用于 Azure 虚拟桌面的 Teams](teams-on-avd.md)。
