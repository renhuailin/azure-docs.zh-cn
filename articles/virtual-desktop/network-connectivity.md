---
title: 了解 Azure 虚拟桌面网络连接性
titleSuffix: Azure
description: 了解 Azure 虚拟桌面网络连接性
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 8a979fa56a7a75785220747dc1ee43696e8897d4
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710516"
---
# <a name="understanding-azure-virtual-desktop-network-connectivity"></a>了解 Azure 虚拟桌面网络连接性

Azure 虚拟桌面提供在会话主机（在 Azure 上运行）上承载客户端会话的功能。 Microsoft 代表客户管理部分服务，并提供用于连接客户端和会话主机的安全终结点。 下图简要概述了 Azure 虚拟桌面使用的网络连接

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Azure 虚拟桌面网络连接示意图" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>会话连接性

Azure 虚拟桌面使用远程桌面协议 (RDP) 通过网络连接提供远程显示和输入功能。 RDP 最初在 Windows NT 4.0 终端服务器版中发布，并在每个 Microsoft Windows 和 Windows Server 版本中不断发展。 从一开始，RDP 就开发为独立于它的底层传输堆栈，现在它支持多种类型的传输。

## <a name="reverse-connect-transport"></a>反向连接传输

Azure 虚拟桌面使用反向连接传输来建立远程会话和携带 RDP 流量。 与本地远程桌面服务部署不同，反向连接传输不使用 TCP 侦听器来接收传入的 RDP 连接， 而是通过 HTTPS 连接使用到 Azure 虚拟桌面基础结构的出站连接。

## <a name="session-host-communication-channel"></a>会话主机信道

在 Azure 虚拟桌面会话主机启动时，远程桌面代理加载器服务会建立 Azure 虚拟桌面中转站的永久性信道。 此信道基于安全的传输层安全性 (TLS) 连接，充当用于在会话主机与 Azure 虚拟桌面基础结构之间交换服务消息的总线。

## <a name="client-connection-sequence"></a>客户端连接顺序

客户端连接顺序如下所述：

1. 用户使用受支持的 Azure 虚拟桌面客户端订阅 Azure 虚拟桌面工作区
2. Azure Active Directory 对用户进行身份验证，并返回用于枚举可供用户使用的资源的令牌
3. 客户端将令牌传递给 Azure 虚拟桌面源订阅服务
4. Azure 虚拟桌面源订阅服务对令牌进行验证
5. Azure 虚拟桌面源订阅服务以数字签名连接配置的形式将可用桌面和 RemoteApp 的列表传递回客户端
6. 客户端在一组 .rdp 文件中存储每个可用资源的连接配置
7. 当用户选择要连接的资源时，客户端会使用关联的 .rdp 文件，并建立与最近的 Azure 虚拟桌面网关实例的安全 TLS 1.2 连接，然后传递连接信息
8. Azure 虚拟桌面网关验证请求并要求 Azure 虚拟桌面中转站来协调连接
9. Azure 虚拟桌面中转站识别会话主机，并使用以前建立的持久信道来初始化连接
10. 远程桌面堆栈启动 TLS 1.2 连接，连接到客户端使用的 Azure 虚拟桌面网关实例
11. 在客户端和会话主机都连接到网关后，网关开始在两个终结点之间转发原始数据，这将为 RDP 建立基本反向连接传输
12. 设置基本传输后，客户端启动 RDP 握手

## <a name="connection-security"></a>连接安全性

TLS 1.2 用于从客户端和会话主机启动的与 Azure 虚拟桌面基础结构组件之间的所有连接。 Azure 虚拟桌面使用与 [Azure Front Door](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-) 相同的 TLS 1.2 密码。 请务必确保客户端计算机和会话主机可以使用这些密码。
对于反向连接传输，客户端和会话主机都会连接到 Azure 虚拟桌面网关。 建立 TCP 连接后，客户端或会话主机会验证 Azure 虚拟桌面网关的证书。
建立基本传输后，RDP 会使用会话主机的证书在客户端与会话主机之间建立一个嵌套的 TLS 连接。 默认情况下，用于 RDP 加密的证书由 OS 在部署过程中自行生成。 如果需要，客户可以部署由企业证书颁发机构颁发的集中管理的证书。 有关如何配置证书的详细信息，请参阅 [Windows Server 文档](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)。

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure 虚拟桌面的带宽要求，请参阅[了解 Azure 虚拟桌面的远程桌面协议 (RDP) 带宽要求](rdp-bandwidth.md)。
* 若要开始使用 Azure 虚拟桌面的服务质量 (QoS)，请参阅[实现 Azure 虚拟桌面的服务质量 (QoS)](rdp-quality-of-service-qos.md)。
