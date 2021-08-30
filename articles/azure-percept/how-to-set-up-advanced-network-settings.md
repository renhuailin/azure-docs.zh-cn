---
title: 在 Azure Percept DK 上设置高级网络设置
description: 本文将为用户逐步介绍 Azure Percept DK 安装体验中的高级网络设置
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 7/19/2021
ms.custom: template-how-to
ms.openlocfilehash: bc680025e37ded3fd91b840d00ba075772cbb656
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727202"
---
# <a name="set-up-advanced-network-settings-on-the-azure-percept-dk"></a>在 Azure Percept DK 上设置高级网络设置

利用 Azure Percept DK，可以控制开发工具包上的各种网络组件。 这是通过设置体验中的高级网络设置实现的。 要访问这些设置，必须[启动设置体验](./quickstart-percept-dk-set-up.md)，然后在“网络连接”页面上选择“访问高级网络设置” 。

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-entry.png" alt-text="从“网络连接”页面启动高级网络设置":::

## <a name="select-the-security-setting"></a>选择安全设置
在 Azure Percept DK 上，本地连接支持 IPv4 和 IPv6。

> [!NOTE]
> Azure IoT 中心[不支持 IPv6](../iot-hub/iot-hub-understand-ip-address.md#support-for-ipv6)。 必须使用 IPv4 与 IoT 中心通信。
1. 选择“IPv4”单选按钮，然后选择“网络设置”下的项，更改其 IPv4 设置
1. 选择“IPv6”单选按钮，然后选择“网络设置”下的项，更改其 IPv6 设置
1. 根据你的选择，“网络设置”选项可能会发生改变

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-security.png" alt-text="选择安全协议以查看网络选项列表":::

## <a name="define-a-static-ip-address"></a>定义静态 IP 地址

1. 在“高级网络设置”页面中，从列表中选择“定义静态 IP 地址” 
1. 从下拉菜单中选择“网络接口”
1. 取消选中“动态 IP 地址”
1. 输入静态 IP 地址
1. 输入子网 IP 地址（也称为子网掩码）
1. 输入网关 IP 地址（也称为默认网关）
1. 如果适用，请输入 DNS 地址
1. 选择“保存”
1. 选择“返回”，以返回“高级网络设置”主页面 

## <a name="define-dns-server-for-docker"></a>为 Docker 定义 DNS 服务器
这些设置可让你修改或添加新的 Docker DNS IP 地址。

> [!NOTE]
> Docker 服务已配置为仅接受 IPv4 DNS 条目。  从 IPv6 屏幕添加的条目将被忽略。

1. 在“高级网络设置”中，从列表中选择“为 Docker 定义 DNS 服务器” 
1. 输入 Docker IPv4 DNS 地址
1. 选择“保存”
1. 选择“返回”，以返回“高级网络设置”主页面 

## <a name="define-bridge-internet-protocol-for-docker"></a>为 Docker 定义网桥 Internet 协议
通过网桥 Internet 协议屏幕，可更改 Docker 容器的 IPv4 地址空间。

如果设备的 IP 地址与 Azure Percept Devkit 的 Docker 服务 (172.17.x.x) 共用相同的路由，则需要将 Docker 的网桥更改为其他路由，以允许 Docker 容器与 Azure IoT 中心之间的通信。  

1. 在“高级网络设置”页面中，从列表中选择“为 Docker 定义网桥 Internet 协议” 
1. 键入 Docker 网桥 Internet 协议 IPv4 地址 (BIP)
1. 选择“保存”
1. 选择“返回”，以返回“高级网络设置”主页面 

## <a name="define-an-internet-proxy-server"></a>定义 Internet 代理服务器
通过此选项，可定义代理服务器。    

1. 在“高级网络设置”页面中，从列表中选择“定义 Internet 代理服务器” 
1. 选中“使用代理服务器”框，以启用该选项。
1. 输入代理服务器的 HTTP 地址（如果适用）
1. 输入代理服务器的 HTTPS 地址（如果适用）
1. 输入代理服务器的 FTP 地址（如果适用）
1. 在“无代理地址”框中，输入不应使用代理服务器的任何 IP 地址
1. 选择“保存”
1. 选择“返回”，以返回“高级网络设置”主页面 

## <a name="setup-zero-touch-provisioning"></a>设置零接触预配

> [!IMPORTANT]
> “设置零接触预配”设置目前不起作用

利用该选项，可将 Azure Percept DK 变为 [Wi-Fi Easy Connect<sup>TM</sup> 批量配置器](https://techcommunity.microsoft.com/t5/internet-of-things/simplify-wi-fi-iot-device-onboarding-with-zero-touch/ba-p/2161129#:~:text=A%20Wi-Fi%20Easy%20Connect%E2%84%A2%20Configurator%2C%20paired%20with%20the,device%20to%20any%20WPA2-Personal%20or%20WPA3-Personal%20wireless%20LAN.)，用于一次性地将多台设备加入到 Wi-Fi 基础结构中。  

## <a name="define-access-point-passphrase"></a>定义接入点密码 
利用该选项，可更新 Azure Percept DK Wi-Fi 接入点密码。  

> [!CAUTION]
> 保存新密码后，将立即断开与 Wi-Fi 接入点的连接。  请使用新密码重新连接，以重新获得访问权限。  

密码要求：
- 长度必须在 12 到 123 个字符之间
- 必须至少包含一个小写字符、一个大写字符、一个数字和一个特殊字符。

1. 在“高级网络设置”页面中，从列表中选择“定义接入点密码” 
1. 输入新密码
1. 选择“保存”
1. 选择“返回”，以返回“高级网络设置”主页面 

## <a name="next-steps"></a>后续步骤
在“高级网络设置”中进行更改后，选择“返回”按钮，[继续完成 Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md) 。