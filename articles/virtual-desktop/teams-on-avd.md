---
title: Azure 虚拟桌面端的 Microsoft Teams - Azure
description: 如何使用 Azure 虚拟桌面端的 Microsoft Teams。
author: Heidilohr
ms.topic: how-to
ms.date: 08/02/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 63c75636fcab47a31735b772f85a8dccbb9c65f5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728452"
---
# <a name="use-microsoft-teams-on-azure-virtual-desktop"></a>使用 Azure 虚拟桌面端的 Microsoft Teams

>[!IMPORTANT]
>Microsoft 365 政府版 (GCC) 和 GCC-High 环境支持针对 Teams 的媒体优化。 Microsoft 365 DoD 不支持针对 Teams 的媒体优化。

>[!NOTE]
>Microsoft Teams 的媒体优化仅适用于 Windows 10 计算机上的 Windows 桌面客户端。 媒体优化需要 Windows 桌面客户端版本 1.2.1026.0 或更高版本。

Azure 虚拟桌面上的 Microsoft Teams 支持聊天和协作。 经媒体优化后，它还支持通话和会议功能。 若要详细了解如何在虚拟桌面基础结构 (VDI) 环境中使用 Microsoft Teams，请参阅[适用于虚拟化桌面基础结构的 Teams](/microsoftteams/teams-for-vdi/)。

借助 Microsoft Teams 的媒体优化，Windows 桌面客户端会在本地针对 Teams 通话和会议处理音频和视频。 你仍可将 Azure 虚拟桌面上的 Microsoft Teams 与其他客户端配合使用，后者无需支持经过优化的通话和会议功能亦无妨。 所有平台都支持 Teams 聊天和协作功能。 若要在远程会话中重定向本地设备，请查看[自定义主机池的远程桌面协议属性](#customize-remote-desktop-protocol-properties-for-a-host-pool)。

## <a name="prerequisites"></a>先决条件

在使用 Azure 虚拟桌面上的 Microsoft Teams 之前，需要执行以下操作：

- 为 Microsoft Teams [准备你的网络](/microsoftteams/prepare-network/)。
- 在 Windows 10 或 Windows 10 IoT 企业设备上安装 [Windows 桌面客户端](./user-documentation/connect-windows-7-10.md)，设备需满足 Microsoft Teams 的[Windows 电脑上 Teams 的硬件要求](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)。
- 连接到 Windows 10 多会话或 Windows 10 企业版虚拟机 (VM)。

## <a name="install-the-teams-desktop-app"></a>安装 Teams 桌面应用

本部分将介绍如何在 Windows 10 多会话或 Windows 10 企业版 VM 映像上安装 Teams 桌面应用。 若要了解详细信息，请参阅[在 VDI 上安装或更新 Teams 桌面应用](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi)。

### <a name="prepare-your-image-for-teams"></a>为 Teams 准备映像

若要为 Teams 启用媒体优化，请在主机上设置以下注册表项：

1. 从开始菜单中，以管理员身份运行“RegEdit”。 导航至 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams。 请创建 Teams 密钥（如果该密钥不存在）。

2. 为 Teams 密钥创建以下值：

| 名称             | 类型   | 数据/值  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>安装 Teams WebSocket 服务

在 VM 映像上安装最新的[远程桌面 WebRTC 重定向程序服务](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWFYsj)。 如果遇到安装错误，请安装[最新的 Microsoft Visual C++ 可再发行程序包](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)，然后重试。

#### <a name="latest-websocket-service-versions"></a>最新 WebSocket 服务版本

下表列出了 WebSocket 服务的最新版本：

|版本        |发布日期  |
|---------------|--------------|
|1.0.2106.14001 |2021/07/29    |
|1.0.2006.11001 |2020/07/28    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10210614001"></a>针对版本 1.0.2106.14001 的更新

提高了 WebRTC 重定向程序服务和 WebRTC 客户端插件之间的连接可靠性。

#### <a name="updates-for-version-10200611001"></a>针对版本 1.0.2006.11001 的更新

- 修复了在通话或会议期间最小化 Teams 应用会导致传入视频丢失的问题。
- 添加了对选择一台监视器以在多监视器桌面会话中共享的支持。

### <a name="install-microsoft-teams"></a>安装 Microsoft Teams

可以使用基于计算机或基于用户的安装来部署 Teams 桌面应用。 若要在 Azure 虚拟桌面环境中安装 Microsoft Teams，请执行以下操作：

1. 下载与环境匹配的 [Teams MSI 包](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。 建议在 64 位操作系统上使用 64 位安装程序。

      > [!IMPORTANT]
      > Teams 桌面客户端版本 1.3.00.21759 的最新更新修复了 Teams 在聊天、频道和日历中显示 UTC 时区的问题。 新版本的客户端将显示远程会话时区。

2. 运行以下命令之一，将 MSI 安装到主机 VM：

    - 按用户安装

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        此过程是默认安装，它将 Teams 安装到 %AppData% 用户文件夹中。 对于非持久设置，Teams 将无法正常进行基于用户的安装。

    - 基于计算机的安装

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        这会将 Teams 安装到 32 位操作系统上的 Program Files (x86) 文件夹，64 位操作系统上的 Program Files 文件夹。 此时，黄金映像设置已完成。 对于非持久设置，需要为每台计算机安装 Teams。

        安装 Teams 时，可能需要设置两个标志：ALLUSER=1 和 ALLUSERS=1 。 了解这些参数之间的区别很重要。  ALLUSER=1 参数仅在 VDI 环境中用于指定每计算机安装。 可以在非 VDI 和 VDI 环境中使用 ALLUSERS=1 参数。 设置此参数时，Teams 计算机范围安装程序会显示在“控制面板”中的“程序和功能”以及“Windows设置”中的“应用和功能”中。 计算机上具有管理员凭据的所有用户都可以卸载 Teams。

        > [!NOTE]
        > 此时，用户和管理员无法针对 Teams 禁用在登录时自动启动。

3. 若要从主机 VM 中卸载 MSI，请运行以下命令：

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      这会根据操作系统环境从 Program Files (x86) 文件夹或 Program Files 文件夹中卸载 Teams。

      > [!NOTE]
      > 如果使用 MSI 设置 ALLUSER=1 安装 Teams，将禁用自动更新。 建议确保至少每月更新一次 Teams。 若要了解有关部署 Teams 桌面应用的详细信息，请参阅[将 Teams 桌面应用部署到 VM](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)。

### <a name="verify-media-optimizations-loaded"></a>验证媒体优化是否已加载

安装 WebSocket 服务和 Teams 桌面应用后，请按照以下步骤验证是否已加载 Teams 媒体优化：

1. 退出并重启 Teams 应用程序。

2. 选择用户配置文件图像，然后选择“关于”。

3. 选择“版本”。

      如果已加载媒体优化，则横幅会显示已优化 Azure 虚拟桌面媒体。 如果横幅显示 Azure 虚拟桌面媒体未连接，请退出 Teams 应用，然后重试。

4. 选择用户配置文件图像，然后选择“设置”。

      如果已加载媒体优化，则将在设备菜单中枚举本地可用的音频设备和相机。 如果菜单显示“远程音频”，请退出 Teams 应用，然后重试。 如果菜单中仍未显示设备，请检查本地电脑上的“隐私设置”。 确保在“设置” > “隐私” > “应用权限”的  设置中，将“允许应用访问你的麦克风”切换为“开启” 。 断开与远程会话的连接，然后重新连接并再次检查音频和视频设备。 若要加入视频通话和会议，还必须授予应用访问相机的权限。

      如果未加载优化，请卸载，然后重新安装 Teams 并再次检查。

## <a name="known-issues-and-limitations"></a>已知问题和限制

在虚拟化环境中使用 Teams 不同于在非虚拟化环境中使用 Teams 。 有关虚拟化环境中 Teams 限制的详细信息，请查看[适用于虚拟化桌面基础结构的 Teams](/microsoftteams/teams-for-vdi#known-issues-and-limitations)。

### <a name="client-deployment-installation-and-setup"></a>客户端部署、安装和设置

- 对于每计算机安装，VDI 上的 Teams 不会像非 VDI Teams 客户端那样自动更新。 若要更新客户端，你需要通过安装新的 MSI 来更新 VM 映像。
- 仅运行 Windows 10 的计算机上的 Windows 桌面客户端支持 Teams 的媒体优化。
- 不支持使用客户端终结点设备上定义的显式 HTTP 代理。

### <a name="calls-and-meetings"></a>通话和会议

- Azure 虚拟桌面环境中的 Teams 桌面客户端不支持创建实时事件，但用户可以加入实时事件。 目前，我们建议你改为在远程会话中通过 [Teams Web 客户端](https://teams.microsoft.com)创建实时事件。
- 通话或会议当前不支持应用程序共享。 桌面会话支持桌面共享。
- 当前不支持授予控制和实施控制。
- Azure 虚拟桌面上的 Teams 每次仅支持一个传入视频输入。 这意味着每当有人尝试共享其屏幕时，都会显示其屏幕，而不是会议主持人的屏幕。
- 由于 WebRTC 限制，传入和传出视频流分辨率限制为 720p。
- Teams 应用不支持对其他设备使用 HID 按钮或 LED 控件。
- VDI 环境当前不支持新会议体验 (NME)。

对于与虚拟化环境无关的 Teams 已知问题，请参阅[组织的 Teams 支持](/microsoftteams/known-issues)。

## <a name="collect-teams-logs"></a>收集 Teams 日志

如果遇到有关 Azure 虚拟桌面环境中 Teams 桌面应用的问题，请在主机 VM 上的 %appdata%\Microsoft\Teams\logs.txt 下收集客户端日志。

如果有关通话和会议的问题，请使用组合键“Ctrl” + “Alt” + “Shift” + “1”   收集 Teams Web 客户端日志。 日志将写入主机 VM 上的 %userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt。

## <a name="contact-microsoft-teams-support"></a>联系 Microsoft Teams 支持

若要联系 Microsoft Teams 支持，请访问 [Microsoft 365 管理中心](/microsoft-365/admin/contact-support-for-business-products)。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自定义主机池的远程桌面协议属性

通过自定义主机池的远程桌面协议 (RDP) 属性（例如多监视器体验或启用麦克风和音频重定向），可以根据用户的需要为用户提供最佳体验。

使用具有媒体优化的 Teams 时，无需启用设备重定向。 如果使用的是没有媒体优化的 Teams，请设置以下 RDP 属性以启用麦克风和相机重定向：

- `audiocapturemode:i:1` 启用本地设备的音频捕获并重定向远程会话中的音频应用程序。
- `audiomode:i:0` 在本地计算机上播放音频。
- `camerastoredirect:s:*` 重定向所有相机。

若要了解详细信息，请参阅[自定义主机池的远程桌面协议属性](customize-rdp-properties.md)。
