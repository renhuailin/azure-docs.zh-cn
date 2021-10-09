---
title: Azure Site Recovery 中的传输层安全性
description: 了解如何使 Azure Site Recovery 能够使用加密协议传输层安全性 (TLS) 在通过网络传输数据时保证数据的安全。
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 3937fd9f88a844c0257bc6cb66b4c3f97a112987
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594384"
---
# <a name="transport-layer-security-in-azure-site-recovery"></a>Azure Site Recovery 中的传输层安全性

传输层安全性 (TLS) 是在通过网络传输数据时保护数据安全的加密协议。 Azure Site Recovery 使用 TLS 来保护正在传输的数据的隐私。 Azure Site Recovery 现在使用 TLS 1.2 协议来提高安全性。

## <a name="enable-tls-on-older-versions-of-windows"></a>在旧版本的 Windows 上启用 TLS

如果计算机运行的是较早版本的 Windows，请确保安装如下详述的相应更新，并按照相应知识库文章中的说明进行注册表更改。

|操作系统  |知识库文章 |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2、Windows 7、Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>更新将安装协议所需的组件。 安装后，若要启用所需的协议，请确保更新上述知识库文章中提到的注册表项。

## <a name="verify-windows-registry"></a>验证 Windows 注册表

### <a name="configure-schannel-protocols"></a>配置 SChannel 协议

以下注册表项确保在 SChannel 组件级别启用 TLS 1.2 协议：

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>默认情况下，上述注册表项设置为 Windows Server 2012 R2 及更高版本中显示的值。 对于这些版本的 Windows，如果缺少注册表项，则无需创建它们。

### <a name="configure-net-framework"></a>配置 .NET Framework

使用以下注册表项来配置支持强加密的 .NET Framework。 详细了解[在此处配置 .NET Framework](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)。

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-enable-tls-12"></a>为什么启用 TLS 1.2？

TLS 1.2 比以前的加密协议（如 SSL 2.0、SSL 3.0、TLS 1.0 和 TLS 1.1）更安全。 Azure Site Recovery 服务完全支持 TLS 1.2。

### <a name="what-determines-the-encryption-protocol-used"></a>什么因素决定所使用的加密协议？

客户端和服务器都支持的最高协议版本会通过协商确定，以便建立加密会话。 有关 TLS 握手协议的详细信息，请参阅[通过使用 TLS 建立安全会话](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)。

### <a name="what-is-the-impact-if-tls-12-is-not-enabled"></a>如果未启用 TLS 1.2，会有什么影响？

为了更好地防御协议降级攻击，Azure Site Recovery 将开始禁用早于 1.2 的 TLS 版本。 这是为了禁止旧协议和密码套件连接而在服务间进行的长期转换过程的一部分。 Azure Site Recovery 服务和组件完全支持 TLS 1.2。 但是，缺少所需更新或某些自定义配置的 Windows 版本仍会阻止提供 TLS 1.2 协议。 这可能会导致失败，其中包括但不限于以下一种或多种情况：

- 复制可能在源中失败。
- Azure Site Recovery 组件连接失败，出现错误 10054（远程主机强行关闭了现有的连接）。
- 与 Azure Site Recovery 相关的服务无法正常停止或启动。

## <a name="additional-resources"></a>其他资源

- [传输层安全协议](/windows/win32/secauthn/transport-layer-security-protocol)
- [确保已部署的操作系统都支持 TLS 1.2](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework 中的传输层安全性 (TLS) 最佳做法](/dotnet/framework/network-programming/tls)