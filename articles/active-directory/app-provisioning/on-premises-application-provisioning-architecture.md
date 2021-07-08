---
title: Azure AD 本地应用程序预配体系结构 | Microsoft Docs
description: 概述本地应用程序预配体系结构。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 696f2f9e54e350b2cce952cbec8d745779e1d00a
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570351"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Azure AD 本地应用程序预配体系结构

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 可在[此处](https://aka.ms/onpremprovisioningpublicpreviewaccess)请求访问功能。 未来几个月在我们准备发布正式版的过程中，我们会向更多客户和连接者开放该预览版。

## <a name="overview"></a>概述

下图显示了本地应用程序预配工作原理的概述。

![体系结构](.\media\on-premises-application-provisioning-architecture\arch-3.png)

有三个主要组件用于在本地应用程序中预配用户。

- 预配代理在 Azure AD 与本地环境之间提供连接。
- ECMA 主机将来自 Azure AD 的预配请求转换为对目标应用程序发出的请求。 它充当 Azure AD 与应用程序之间的网关。 通过该主机可以导入与 Microsoft Identity Manager 配合使用的现有 ECMA2 连接器。 请注意，如果你构建了 SCIM 应用程序或 SCIM 网关，则不需要 ECMA 主机。
- Azure AD 预配服务充当同步引擎。

>[!NOTE]
> MIM 同步不是必需的。 但是，你可以先使用 MIM 同步来构建并测试 ECMA 连接器，然后再将其导入 ECMA 主机。


### <a name="firewall-requirements"></a>防火墙要求

无需与企业网络建立入站连接。 预配代理仅使用与预配服务建立的出站连接，这意味着，无需为传入连接打开防火墙端口。 此外，无需部署外围网络 (DMZ)，因为所有连接都是出站的，并且是通过安全通道建立的。 

## <a name="agent-best-practices"></a>代理最佳做法
- 确保自动 Azure AD Connect 预配代理自动更新服务正在运行。 安装代理时，默认会启用该服务。 Microsoft 要求启用自动更新，这样才能为你的部署提供支持。
- 避免对代理与 Azure 之间的出站 TLS 通信进行任何形式的内联检查。 这种内联检查会导致通信流降级。
- 代理必须与 Azure 和应用程序通信，因此，代理的位置会影响这两个连接的延迟时间。 可通过优化每个网络连接来尽量降低端到端流量的延迟。 可通过以下方式优化每个连接：-
- 减少跃点两个端点之间的距离。
- 选择要遍历的适当网络。 例如，遍历专用网络而不遍历公共 Internet 可能速度更快，因为前者是专用链路。

## <a name="provisioning-agent-questions"></a>预配代理问题
**预配代理的正式版是什么？**

请参阅[Azure AD Connect 预配代理：版本发行历史记录](provisioning-agent-release-version-history.md)，了解预配代理的最新正式发行版本。

**如何知道我的预配代理的版本？**

 1. 登录到安装有预配代理的 Windows Server。
 2. 转到“控制面板”->“卸载或更改程序”菜单
 3. 查找与条目“Microsoft Azure AD Connect 预配代理”相对应的版本

**Microsoft 是否会自动拉取预配代理更新？**

会，如果 Windows 服务“Microsoft Azure AD Connect 代理更新程序”启动并运行，则 Microsoft 会自动更新预配代理。 若要在排查问题时获得支持，需确保代理是最新的。

是否可以在运行 Azure AD Connect 或 Microsoft Identity Manager (MIM) 的同一服务器上安装预配代理？

是，可以在运行 Azure AD Connect 或 MIM 的同一服务器上安装预配代理，但不一定非要这样做。

**如何将预配代理配置为使用代理服务器来处理出站 HTTP 通信？**

预配代理支持使用出站代理。 可通过编辑代理配置文件“C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config”对其进行配置。在文件的末尾添加以下行，恰好添加在尾随的 </configuration> 标记前面。 将变量 [proxy-server] 和 [proxy-port] 替换为代理服务器名称和端口值。
```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
**如何确保预配代理能够与 Azure AD 租户进行通信且防火墙不阻止代理所需的端口？**

还可以检查所有必需端口是否已打开。

**如何卸载预配代理？**
1. 登录到安装有预配代理的 Windows Server。
2. 转到“控制面板”->“卸载或更改程序”菜单
3. 卸载以下程序：
     - Microsoft Azure AD Connect 预配代理
     - Microsoft Azure AD Connect 代理更新程序
     - Microsoft Azure AD Connect 预配代理包


## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
