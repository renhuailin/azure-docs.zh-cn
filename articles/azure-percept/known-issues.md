---
title: Azure Percept 的已知问题
description: 详细了解 Azure Percept 的已知问题及其解决方法
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a04e53c8444a01bc42f3ce71393fc842f3419e74
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193402"
---
# <a name="known-issues"></a>已知问题

如果遇到这些问题，则无需打开 bug。 如果遇到任何解决方法问题，请提出问题。

|区域|问题说明|解决方法|
|-------|---------|---------|
| 入职体验 | 如果未配置设备的 Wi-Fi，则无法完成内置体验 () 的 Azure 登录失败。 | 1. 将 SSH 连接到设备访问点 (10.1.1.1)  <br> 2. 识别并复制设备以太网 IP 地址 <br> 3. 使用复制的基于 IP 的以太网 URL 连接到点播体验 |
| 入职体验 | 有时，在使用时，单击 EULA 中的链接将不会打开新网页。 | 复制该链接，并在单独的浏览器窗口中打开它。 |
| 入职体验 | 当连接到移动 Wi-Fi 热点时，无法通过使用体验。 | 将设备直接连接到 SoftAP、Wi-Fi 网络或通过以太网连接到网络。 |
| Wi-fi/SoftAP | SoftAP 有时会断开或消失。 | 我们正在调查。  重新启动设备通常会恢复。 |
| Wi-Fi | 有时打开和关闭 Wi-Fi SoftAP 的硬件按钮不起作用。 | 继续尝试按下按钮或重新启动设备。 |
| Wi-Fi | 用户在连接到 Wi-Fi 时可能会看到一条消息，指出 "此 Wi-Fi 网络使用较旧的安全标准。" | Devkit 的热点/SoftAP 使用 WEP 加密算法。 |
| Wi-Fi | 无法从 Windows 10 PC 连接到 SoftAP，出现以下错误消息： <br> "无法连接到此网络" | 重新启动 devkit 和计算机。 |
| 设备更新 | 在 OTA 更新后，容器不会运行。 | 通过 SSH 连接到设备，并通过此命令重新启动 IoT Edge 容器 `systemctl restart iotedge` 。 这将重新启动所有容器。 |
| 设备更新 | 用户可能会收到一条消息，指出更新失败，即使成功。 | 在 IoT 中心导航到设备的设备克隆，确认设备已更新。 此项在第一次更新后已修复。 |
| 设备更新 | 用户首次更新后，可能会丢失其 Wi-Fi 连接设置。 | 请在更新后通过 "使用时体验" 运行来设置 Wi-Fi 连接。 此项在第一次更新后已修复。 |
| 设备更新 | 执行 OTA 更新后，用户将无法再使用以前创建的用户帐户通过 SSH 登录，并且不能通过此点播体验创建新的 SSH 用户。 此问题影响从以下预安装映像版本执行 OTA 更新的系统：2020.110.114.105 和2020.109.101.105。 | 若要恢复用户配置文件，请在 OTA 更新后执行以下步骤： <br> 使用 "root" 作为用户名，通过[SSH 连接到 devkit](./how-to-ssh-into-percept-dk.md) 。 如果通过 "加入体验" 禁用了 SSH "root" 用户登录，则必须重新启用它。 成功连接后，运行以下命令： <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> 若要恢复以前的用户家庭数据，请运行以下命令： <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| 设备更新 | 执行 OTA 更新后，更新组将丢失。 | 按照以下 [说明](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air#create-a-device-update-group)更新设备的标记。 |
| 开发工具包安装程序 | 如果 Docker 未在系统上正常运行，则可选的 Caffe 安装可能会失败。 | 请确保 Docker 已安装并正在运行，然后重试 Caffe 安装。 |
| 开发工具包安装程序 | 可选的 CUDA 在不兼容的系统上安装失败。 | 运行安装程序之前，请验证系统与 CUDA 的兼容性。 |
| Docker、Network、IoT Edge | 如果内部网络使用 172. x. x. x. x. x. x. x. x. x. x. x x x x. x | 将特殊的 bip 节添加到文件上的/etc/docker/daemon.js，如下所示： `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Azure Percept Studio 中的 "查看流" 链接不会打开显示设备 web 流的新窗口。 | 1. 打开 [Azure 门户](https://portal.azure.com) 并选择 **IoT 中心**。 <br> 2. 单击设备连接到的 IoT 中心。 <br> 3. 在 IoT 中心页面上的 "**自动设备管理**" 下选择 **IoT Edge** 。 <br> 4. 从列表中选择你的设备。 <br> 5. 在设备页面顶部选择 " **设置模块** "。 <br> 6. 单击 " **HostIpModule** " 旁边的垃圾桶图标删除该模块。 <br> 7. 若要确认该操作，请单击 " **查看 + 创建** "，然后单击 " **创建**"。 <br> 8. 打开 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) ，然后单击左侧菜单面板上的 " **设备** "。 <br> 9. 从列表中选择你的设备。 <br> 10. 在 " **远景** " 选项卡上，单击 " **查看设备流**"。 你的设备将下载新版 HostIpModule，并使用设备的 web 流打开浏览器选项卡。 |