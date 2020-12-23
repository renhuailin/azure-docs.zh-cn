---
title: 了解 Windows 虚拟桌面网络连接
titleSuffix: Azure
description: 了解 Windows 虚拟桌面网络连接
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 4c0017a36d84973a4d99c49a5ea33faeb189b35f
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639185"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>了解 Windows 虚拟桌面网络连接

Windows 虚拟桌面提供了在 Azure 上运行的会话主机上承载客户端会话的功能。 Microsoft 代表客户管理部分服务，并为连接客户端和会话主机提供安全终结点。 下图提供了 Windows 虚拟桌面使用的网络连接的高级概述

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Windows 虚拟桌面网络连接示意图" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>会话连接性

Windows 虚拟桌面使用远程桌面协议 (RDP) 提供网络连接的远程显示和输入功能。 RDP 最初是通过 Windows NT 4.0 终端服务器版本发布的，并且在每个 Microsoft Windows 和 Windows Server 版本中不断发展。 从一开始，已开发的 RDP 独立于其基础传输堆栈，并且如今它支持多种传输类型。

## <a name="reverse-connect-transport"></a>反向连接传输

Windows 虚拟桌面使用反向连接传输来建立远程会话和用于传输 RDP 通信。 与本地远程桌面服务部署不同，反向连接传输不使用 TCP 侦听器来接收传入的 RDP 连接。 相反，它使用通过 HTTPS 连接到 Windows 虚拟桌面基础结构的出站连接。

## <a name="session-host-communication-channel"></a>会话主机通信通道

Windows 虚拟桌面会话主机启动时，远程桌面代理加载器服务将建立 Windows 虚拟桌面代理的永久性通信通道。 此信道基于安全传输层安全 (TLS) 连接，并充当会话主机和 Windows 虚拟桌面基础结构之间的服务消息交换的总线。

## <a name="client-connection-sequence"></a>客户端连接顺序

下面描述的客户端连接顺序：

1. 使用受支持的 Windows 虚拟桌面客户端用户订阅 Windows 虚拟桌面工作区
2. Azure Active Directory 对用户进行身份验证，并返回用于枚举用户可用资源的标记
3. 客户端将令牌传递到 Windows 虚拟桌面源订阅服务
4. Windows 虚拟桌面源订阅服务验证令牌
5. Windows 虚拟桌面源订阅服务将可用桌面列表和 RemoteApps 以数字签名的连接配置的形式传递回客户端
6. 客户端为 .rdp 文件集中的每个可用资源存储连接配置
7. 当用户选择要连接的资源时，客户端会使用关联的 .rdp 文件，并建立与最近的 Windows 虚拟桌面网关实例的安全 TLS 1.2 连接，并传递连接信息
8. Windows 虚拟桌面网关验证请求并请求 Windows 虚拟桌面代理协调连接
9. Windows 虚拟桌面 broker 识别会话主机，并使用以前建立的持久通信通道初始化连接
10. 远程桌面堆栈启动与客户端使用的相同 Windows 虚拟桌面网关实例的 TLS 1.2 连接
11. 将客户端和会话主机连接到网关后，网关会开始在两个终结点之间中继原始数据，这将为 RDP 建立基本的反向连接传输
12. 设置基本传输后，客户端将启动 RDP 握手

## <a name="connection-security"></a>连接安全性

TLS 1.2 用于从客户端和会话主机启动到 Windows 虚拟桌面基础结构组件的所有连接。
对于反向连接传输，客户端和会话主机均连接到 Windows 虚拟桌面网关。 建立 TCP 连接后，客户端或会话主机将验证 Windows 虚拟桌面网关的证书。
建立基本传输后，RDP 将使用会话主机的证书在客户端和会话主机之间建立嵌套的 TLS 连接。 默认情况下，用于 RDP 加密的证书是在部署过程中由操作系统自行生成的。 如果需要，客户可以部署企业证书颁发机构颁发的集中管理的证书。 有关配置证书的详细信息，请参阅 [Windows Server 文档](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Windows 虚拟桌面的带宽要求，请参阅 [了解远程桌面协议 (RDP) Windows 虚拟桌面的带宽要求](rdp-bandwidth.md)。
* 若要开始了解 Windows 虚拟桌面的服务质量 (QoS) ，请参阅 [实现 Windows 虚拟桌面 (qos) 的服务质量](rdp-quality-of-service-qos.md)。
