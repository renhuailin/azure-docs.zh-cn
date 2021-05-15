---
title: 配置设备重定向 - Azure
description: 如何配置 Windows 虚拟桌面的设备重定向。
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f603a4b44b555382e4178d90522ae41fa2388663
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107947710"
---
# <a name="configure-device-redirections"></a>配置设备重定向

在为 Windows 虚拟桌面环境配置设备重定向后，可以在远程会话中使用打印机、USB 设备、麦克风和其他外围设备。 有些设备重定向需要对远程桌面协议 (RDP) 属性和组策略设置都进行更改。

## <a name="supported-device-redirections"></a>支持的设备重定向

每个客户端支持不同的设备重定向。 请查看[比较客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare)，以获取每个客户端支持的设备重定向的完整列表。

## <a name="customizing-rdp-properties-for-a-host-pool"></a>为主机池自定义 RDP 属性

若要详细了解如何使用 PowerShell 或 Azure 门户为主机池自定义 RDP 属性，请查看 [RDP 属性](customize-rdp-properties.md)。 有关受支持的 RDP 属性的完整列表，请参阅[受支持的 RDP 文件设置](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)。

## <a name="setup-device-redirections"></a>设置设备重定向

可以使用以下 RDP 属性和组策略设置来配置设备重定向。

### <a name="audio-input-microphone-redirection"></a>音频输入（麦克风）重定向

请设置以下 RDP 属性，以配置音频输入重定向：

- `audiocapturemode:i:1` 启用音频输入重定向。
- `audiocapturemode:i:0` 禁用音频输入重定向。

### <a name="audio-output-speaker-redirection"></a>音频输出（扬声器）重定向

请设置以下 RDP 属性，以配置音频输出重定向：

- `audiomode:i:0` 启用音频输出重定向。
- `audiomode:i:1` 或 `audiomode:i:2` 禁用音频输出重定向。

### <a name="camera-redirection"></a>相机重定向

请设置以下 RDP 属性，以配置相机重定向：

- `camerastoredirect:s:*` 重定向所有相机。
- `camerastoredirect:s:` 禁用相机重定向。

>[!NOTE]
>即使已禁用 `camerastoredirect:s:` 属性，也可以通过 `devicestoredirect:s:` 属性来重定向本地相机。 若要完全禁用相机重定向，请设置 `camerastoredirect:s:`，然后设置 `devicestoredirect:s:` 或者定义一部分不包括任何相机的即插即用设备。

还可以使用以分号分隔的 KSCATEGORY_VIDEO_CAMERA 接口列表来重定向特定的相机，例如 `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi`。

### <a name="clipboard-redirection"></a>剪贴板重定向

请设置以下 RDP 属性，以配置剪贴板重定向：

- `redirectclipboard:i:1` 启用剪贴板重定向。
- `redirectclipboard:i:0` 禁用剪贴板重定向。

### <a name="com-port-redirections"></a>COM 端口重定向

请设置以下 RDP 属性，以配置 COM 端口重定向：

- `redirectcomports:i:1` 启用 COM 端口重定向。
- `redirectcomports:i:0` 禁用 COM 端口重定向。

### <a name="usb-redirection"></a>USB 重定向

首先，请设置以下 RDP 属性，以启用 USB 设备重定向：

- `usbdevicestoredirect:s:*` 启用 USB 设备重定向。
- `usbdevicestoredirect:s:` 禁用 USB 设备重定向。

另外，请在用户的本地设备上设置以下组策略：

- 导航到“计算机配置” > “策略”> “管理模板” > “Windows 组件” > “远程桌面服务” > “远程桌面连接客户端” > “RemoteFX USB 设备重定向”      。
- 选择“允许此计算机中其他受支持的 RemoteFX USB 设备的 RDP 重定向”。
- 选择“启用”选项，然后选择“在RemoteFX USB 重定向访问权限中的管理员和用户”框 。
- 选择“确定”。

### <a name="plug-and-play-device-redirection"></a>即插即用设备重定向

请设置以下 RDP 属性，以配置即插即用设备重定向：

- `devicestoredirect:s:*` 启用所有即插即用设备的重定向。
- `devicestoredirect:s:` 禁用即插即用设备的重定向。

还可以使用以分号分隔的列表来选择特定的即插即用设备，例如 `devicestoredirect:s:root\*PNP0F08`。

### <a name="local-drive-redirection"></a>本地驱动器重定向

请设置以下 RDP 属性，以配置本地驱动器重定向：

- `drivestoredirect:s:*` 启用所有磁盘驱动器的重定向。
- `drivestoredirect:s:` 禁用本地驱动器重定向。

还可以使用以分号分隔的列表来选择特定的驱动器，例如 `drivestoredirect:s:C:;E:;`。

若要启用 Web 客户端文件传输，请设置 `drivestoredirect:s:*`。 如果为此 RDP 属性设置了任何其他值，则将禁用 Web 客户端文件传输。

### <a name="printer-redirection"></a>打印机重定向

请设置以下 RDP 属性，以配置打印机重定向：

- `redirectprinters:i:1` 启用打印机重定向。
- `redirectprinters:i:0` 禁用打印机重定向。

### <a name="smart-card-redirection"></a>智能卡重定向

请设置以下 RDP 属性，以配置智能卡重定向：

- `redirectsmartcards:i:1` 启用智能卡重定向。
- `redirectsmartcards:i:0` 禁用智能卡重定向。
