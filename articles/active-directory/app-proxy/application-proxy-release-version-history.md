---
title: Azure Active Directory 应用程序代理：版本发布历史记录
description: 本文列出了 Azure Active Directory 应用程序代理的所有版本，并介绍了新功能和已修复的问题。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/27/2021
ms.subservice: app-proxy
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c340d0b51e1d4326bba575094491cdd5ea756616
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108764380"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 应用程序代理：版本发布历史记录
本文列出了已发布的 Azure Active Directory (Azure AD) 应用程序代理的版本和功能。 Azure AD 团队会定期更新应用程序代理的新特性和功能。 应用程序代理连接器会[在新的主版本发布时自动更新](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-)。 

建议确保为连接器启用了自动更新，以确保具有最新的功能和 bug 修复。 Microsoft 支持部门可能会要求你安装最新的连接器版本以解决问题。

下面是相关资源的列表：

| 资源                                         | 详细信息                                                      |
| ------------------------------------------------ | ------------------------------------------------------------ |
| 如何启用应用程序代理                  | 本[教程](application-proxy-add-on-premises-application.md)介绍了启用应用程序代理以及安装和注册连接器的先决条件。 |
| 了解 Azure AD 应用程序代理连接器 | 了解有关[连接器管理](application-proxy-connectors.md)以及连接器[自动升级](application-proxy-connectors.md#automatic-updates)方式的详细信息。 |
| Azure AD 应用程序代理连接器下载    | [下载最新的连接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。 |

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>版本状态

2020 年 7 月 22 日：已发布供下载。此版本仅可通过下载页面安装。 

### <a name="new-features-and-improvements"></a>新增功能和改进
-   改进了对 Azure 政府云环境的支持。 有关如何正确安装 Azure 政府云连接器的步骤，请查看[先决条件](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls)和[安装步骤](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)。
- 支持将远程桌面服务 Web 客户端与应用程序代理配合使用。 有关详细信息，请参阅[使用 Azure AD 应用程序代理发布远程桌面](application-proxy-integrate-with-remote-desktop-services.md)。
- 改进了 websocket 扩展协商。 
- 支持连接器组和应用程序代理云服务之间基于区域的优化路由。 有关详细信息，请参阅[使用 Azure Active Directory 应用程序代理优化通信流](application-proxy-network-topology.md)。 

### <a name="fixed-issues"></a>已修复的问题
- 修复了强制小写字符串的 websocket 问题。
- 修复了导致连接器偶尔无响应的问题。

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>版本状态

2020 年 7 月 17 日：已发布供下载。 此版本仅可通过下载页面安装。 

### <a name="fixed-issues"></a>已修复的问题
- 解决了以前版本中存在的内存泄漏问题
- websocket 支持的常规改进

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>版本状态

2020 年 4 月 7 日：已发布供下载。此版本仅可通过下载页面安装。 

### <a name="new-features-and-improvements"></a>新增功能和改进
-   连接器对所有连接仅使用 TLS 1.2。 有关详细信息，请参阅[连接器先决条件](application-proxy-add-on-premises-application.md#prerequisites)。
- 改进了连接器与 Azure 服务之间的信号。 这包括支持连接器与 Azure 服务之间的 WCF 通信的可靠会话，以及针对 WebSocket 通信的 DNS 缓存改进。
- 支持在连接器与后端应用程序之间配置代理。 有关详细信息，请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="fixed-issues"></a>已修复的问题
- 删除了到端口 8080 的回退，以允许连接器与 Azure 服务进行通信。
- 为 WebSocket 通信添加了调试跟踪。 
- 解决了在后端应用程序 cookie 上设置时保留 SameSite 属性的问题。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>版本状态

2018 年 9 月 20 日：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 添加了对 QlikSense 应用程序的 WebSocket 支持。 若要了解有关如何将 QlikSense 与应用程序代理集成的详细信息，请参阅此[演练](application-proxy-qlik.md)。 
- 改进了安装向导，使其更易于配置出站代理。 
- 将 TLS 1.2 设置为连接器的默认协议。 
- 添加了新的最终用户许可协议 (EULA)。  

### <a name="fixed-issues"></a>已修复的问题

- 修复了导致连接器中内存泄漏的 bug。
- 更新了 Azure 服务总线版本，其中包括针对连接器超时问题的 bug 修复。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>版本状态

2018 年 1 月 19 日：已发布供下载

### <a name="fixed-issues"></a>已修复的问题

- 添加了对需要 cookie 中域转换的自定义域的支持。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>版本状态 

2017 年 5 月 25 日：已发布供下载 

### <a name="new-features-and-improvements"></a>新增功能和改进 

提高了对连接器的出站连接限制的控制。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>版本状态

2017 年 4 月 15 日：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 简化了加入和管理，所需的端口更少。 应用程序代理现在仅需要打开两个标准出站端口：443 和 80。 应用程序代理将继续仅使用出站连接，因此，仍然不需要 DMZ 中的任何组件。 有关详细信息，请参阅我们的 [配置文档](application-proxy-add-on-premises-application.md)。  
- 如果由外部代理或防火墙提供支持，则现在可以通过 DNS 而不是 IP 范围打开网络。 应用程序代理服务仅需要连接到 *.msappproxy.net 和 *.servicebus.windows.net。


## <a name="earlier-versions"></a>早期版本

如果使用的应用程序代理连接器版本低于 1.5.36.0，请更新到最新版本，以确保具有最新的完全受支持的功能。

## <a name="next-steps"></a>后续步骤
- 详细了解[通过 Azure AD 应用程序代理远程访问本地应用程序](application-proxy.md)。
- 若要开始使用应用程序代理，请参阅[教程：添加本地应用程序以通过应用程序代理远程访问](application-proxy-add-on-premises-application.md)。
