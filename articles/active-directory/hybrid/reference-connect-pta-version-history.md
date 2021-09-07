---
title: Azure AD 直通身份验证：版本发布历史记录 | Microsoft Docs
description: 本文列出了 Azure AD 直通身份验证代理的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 852a842c9b58a76742d0b482e62c49cd91d34f1b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099640"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 直通身份验证代理：版本发布历史记录 
 
本地安装的可启用直通身份验证的代理会定期进行更新，以便提供新功能。 本文列出了在引入新功能时添加的版本和功能。 发布新版本时，直通身份验证代理会自动进行更新。 

以下是相关主题： 

- [使用 Azure AD 直通身份验证的用户登录](how-to-connect-pta.md) 
- [Azure AD 直通身份验证代理安装](how-to-connect-pta-quick-start.md) 

## <a name="1524820"></a>1.5.2482.0
### <a name="release-status"></a>版本状态： 
2021/07/07：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 已将包/库升级到使用 SHA-256RSA 进行签名的较新版本。

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>版本状态： 
2020/04/09：已发布，供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 添加了在安装后针对云环境提供的支持。 捆绑包可以固定到给定的云环境。



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>版本状态 
2019/1/22：已发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 添加了对服务总线可靠通道的支持，目的是为出站连接添加另一层连接复原能力 
- 在代理注册过程中强制实施 TLS 1.2 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>版本状态 
2018/4/10：已发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- Web 套接字连接支持 
- 将 TLS 1.2 设置为代理的默认协议。 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>版本状态 
2018/1/31：已发布供下载  
### <a name="fixed-issues"></a>已修复的问题 
- 修复了导致代理中出现内存泄漏的 bug。 
- 更新了 Azure 服务总线版本，其中包括针对连接器超时问题的 bug 修复。 
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 添加了对代理和 Azure AD 服务之间基于 websocket 的连接的支持，以提高连接复原能力

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>版本状态 
2017/11/25：已发布供下载  
### <a name="fixed-issues"></a>已修复的问题 
- 修复了与默认代理方案的 DNS 缓存相关的 bug 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>版本状态 
2017/10/17：已发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 为出站连接添加了 DNS 缓存功能，以增加从 DNS 故障进行复原的能力 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>版本状态 
2017/08/31：已发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- Azure AD 直通身份验证代理的正式发布版本 

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 直通身份验证的用户登录](how-to-connect-pta.md)
