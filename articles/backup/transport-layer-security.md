---
title: Azure 备份中的传输层安全性
description: 了解如何使 Azure 备份能够使用加密协议传输层安全性 (TLS) 在通过网络传输数据时保证数据的安全。
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327111"
---
# <a name="transport-layer-security-in-azure-backup"></a>Azure 备份中的传输层安全性

传输层安全性 (TLS) 是在通过网络传输数据时保护数据安全的加密协议。 Azure 备份使用传输层安全性来保护正在传输的备份数据的隐私。 本文介绍了启用 TLS 1.2 协议的步骤，该协议提供了比以前版本更高的安全性。

## <a name="earlier-versions-of-windows"></a>早期版本的 Windows

如果计算机运行的是早期版本的 Windows，则必须安装下述相应更新，并且必须应用知识库文章中介绍的注册表更改。

|操作系统  |知识库文章 |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2、Windows 7、Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>该更新将安装所需协议组件。 安装完成后，必须进行上述知识库文章中提到的注册表项更改，以正确启用所需协议。

## <a name="verify-windows-registry"></a>验证 Windows 注册表

### <a name="configuring-schannel-protocols"></a>配置 SChannel 协议

以下注册表项确保在 SChannel 组件级别启用 TLS 1.2 协议：

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>显示的值在 Windows Server 2012 R2 及更高版本中是默认设置的。 对于这些版本的 Windows，如果注册表项不存在，则无需创建它们。

### <a name="configuring-net-framework"></a>配置 .NET Framework

以下注册表项将 .NET Framework 配置为支持强加密。 可以[在此处详细了解如何配置 .NET Framework](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)。

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

TLS 1.2 比以前的加密协议（如 SSL 2.0、SSL 3.0、TLS 1.0 和 TLS 1.1）更安全。 Azure 备份服务已完全支持 TLS 1.2。

### <a name="what-determines-the-encryption-protocol-used"></a>什么因素决定所使用的加密协议？

客户端和服务器都支持的最高协议版本会通过协商确定，以便建立加密会话。 有关 TLS 握手协议的详细信息，请参阅[通过使用 TLS 建立安全会话](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)。

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>未启用 TLS 1.2 会有什么影响？

为了更好地防御协议降级攻击，Azure 备份将以分阶段的方式开始禁用早于 1.2 的 TLS 版本。 这是为了禁止旧协议和密码套件连接而在服务间进行的长期转换过程的一部分。 Azure 备份服务和组件完全支持 TLS 1.2。 但是，缺少所需更新或某些自定义配置的 Windows 版本仍会阻止提供 TLS 1.2 协议。 这可能会导致失败，其中包括但不限于以下一种或多种情况：

- 备份和还原操作可能会失败。
- 备份组件连接失败，出现错误 10054（远程主机强行关闭了现有的连接）。
- 与 Azure 备份相关的服务无法正常停止或启动。

## <a name="additional-resources"></a>其他资源

- [传输层安全协议](/windows/win32/secauthn/transport-layer-security-protocol)
- [确保已部署的操作系统都支持 TLS 1.2](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework 中的传输层安全性 (TLS) 最佳做法](/dotnet/framework/network-programming/tls)