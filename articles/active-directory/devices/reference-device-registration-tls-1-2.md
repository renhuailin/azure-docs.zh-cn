---
title: 强制执行 TLS 1.2 - Azure Active Directory 注册服务
description: 删除对 Azure AD 设备注册服务的 TLS 1.0 和 1.1 的支持
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a36d856252f3ff63fd909d63f254a15930666e8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616035"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>对 Azure AD 注册服务强制执行 TLS 1.2

Azure Active Directory (Azure AD) 设备注册服务用于通过设备标识将设备连接到云。 Azure AD 设备注册服务目前支持使用传输层安全性 (TLS) 1.2 与 Azure 进行通信。 为确保安全性与最佳加密，Microsoft 建议禁用 TLS 1.0 和 1.1。 本文档将提供以下相关信息：如何确保用于完成注册和与 Azure AD 设备注册服务通信的计算机使用 TLS 1.2。

TLS 协议版本 1.2 是一种旨在提供安全通信的加密协议。 TLS 协议主要目的在于提供隐私和数据完整性。 TLS 经历了多次迭代，版本 1.2 在 [RFC 5246（外部链接）](https://tools.ietf.org/html/rfc5246)中进行定义。

当前的连接分析显示 TLS 1.1 和 1.0 使用极少，但我们提供了此信息，以便你在对 TLS 1.1 和 1.0 的支持终止之前根据需要更新所有受影响的客户端或服务器。 如果要将任何本地基础结构用于混合场景或 Active Directory 联合身份验证服务 (AD FS)，请确保该基础结构可以同时支持使用 TLS 1.2 的入站和出站连接。

## <a name="update-windows-servers"></a>更新 Windows 服务器

对于使用 Azure AD 设备注册服务或充当代理的 Windows 服务器，请使用以下步骤确保启用 TLS 1.2：

> [!IMPORTANT]
> 更新注册表后，必须重启 Windows 服务器才能使更改生效。

### <a name="enable-tls-12"></a>启用 TLS 1.2

确保按所示方式配置以下注册表字符串：

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client
  - "DisabledByDefault"=dword:00000000
  - "Enabled"=dword:00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server
  - "DisabledByDefault"=dword:00000000
  - "Enabled"=dword:00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>更新非 Windows 代理

在设备和 Azure AD 设备注册服务之间充当代理的任何计算机必须确保已启用 TLS 1.2。 遵循供应商的指南以确保支持。

## <a name="update-ad-fs-servers"></a>更新 AD FS 服务器

任何用于与 Azure AD 设备注册服务通信的 AD FS 服务器都必须确保已启用 TLS 1.2。 有关如何启用/验证此配置的信息，请参阅[为 AD FS 管理 SSL/TLS 协议和密码套件](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)。

## <a name="client-updates"></a>客户端更新

由于所有客户端服务器和浏览器服务器组合都必须使用 TLS 1.2 才能与 Azure AD 设备注册服务连接，因此你可能需要更新这些设备。

已知以下客户端无法支持 TLS 1.2。 请更新客户端，确保访问不中断。

- Android 版本 4.3 及更早版本
- Firefox 版本 5.0 及更早版本
- Windows 7 及更早版本上的 Internet Explorer 版本 8-10
- Windows Phone 8.0 上的 Internet Explorer 10
- OS X 10.8.4 及更早版本上的 Safari 版本 6.0.4

## <a name="next-steps"></a>后续步骤

[TLS/SSL 概述 (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)