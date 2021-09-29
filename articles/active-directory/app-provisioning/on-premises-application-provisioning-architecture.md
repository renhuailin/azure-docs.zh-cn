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
ms.openlocfilehash: cdd7995c50ef63b4ec88e65c949a4c098a4b9330
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609928"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Azure AD 本地应用程序预配体系结构

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)。 在未来准备正式发布 (GA) 的几个月里，我们将向更多客户和连接器开放该预览版。

## <a name="overview"></a>概述

下图展示了本地应用程序预配流程的概况。

![此图展示了本地应用程序预配的体系结构。](.\media\on-premises-application-provisioning-architecture\arch-3.png)

有三个主要组件用于在本地应用程序中预配用户：

- 预配代理在 Azure Active Directory (Azure AD) 与本地环境之间提供连接。
- ECMA 主机将来自 Azure AD 的预配请求转换为对目标应用程序发出的请求。 它充当 Azure AD 与应用程序之间的网关。 你可以使用它来导入与 Microsoft Identity Manager 配合使用的现有 ECMA2 连接器。 如果已构建了 SCIM 应用程序或 SCIM 网关，则不需要 ECMA 主机。
- Azure AD 预配服务充当同步引擎。

>[!NOTE]
> 不需要 Microsoft Identity Manager 同步。 但是，你可以先使用它来构建并测试 ECMA 连接器，然后再将其导入 ECMA 主机。


### <a name="firewall-requirements"></a>防火墙要求

无需向企业网络打开入站连接。 预配代理仅使用与预配服务建立的出站连接，这意味着，无需为传入连接打开防火墙端口。 此外，无需部署外围网络 (DMZ)，因为所有连接都是出站的，并且是通过安全通道建立的。

## <a name="ecma-connector-host-architecture"></a>ECMA 连接器主机体系结构
ECMA 连接器主机将多方面的内容用于实现本地预配。  下图是一个概念图，显示了各个方面的内容。  下表更详细地描述了这些方面。

[![ECMA 连接器主机](.\media\on-premises-application-provisioning-architecture\ecma-2.png)](.\media\on-premises-application-provisioning-architecture\ecma-2.png#lightbox)



|区域|说明|
|-----|-----|
|终结点|负责与 Azure AD 预配服务通信和数据传输|
|内存中缓存|用于存储从本地数据源导入的数据|
|Autosync|在 ECMA 连接器主机与本地数据源之间提供异步数据同步|
|业务逻辑|用于协调所有 ECMA 连接器主机活动。  ECMA 主机中可配置自动同步时间。 这位于“属性”页。|

### <a name="about-anchor-attributes-and-distinguished-names"></a>关于定位点属性和可分辨名称
提供以下信息是为了更好地介绍定位点属性和可分辨名称，尤其是 genericSQL 连接器使用的名称。

定位点属性是对象类型的唯一属性，该属性不会发生变化，并可在 ECMA 连接器主机内存的缓存中表示该对象。

可分辨名称 (DN) 指示对象在目录层次结构中的当前位置，是对象的唯一标识名称。  如果是 SQL，则是分区中的位置。 该名称通过将定位点属性与目录分区的根连接在一起而形成。 

当我们思索传统格式的传统 DN 时（例如针对 Active Directory 或 LDAP），我们所想到的内容类似于：

  CN=Lola Jacobson,CN=Users,DC=contoso,DC=com

但是，对于 SQL 等数据源（该数据源是平的，而不具层次机构），DN 需要已存在于其中一个表中，或根据我们向 ECMA 连接器主机提供的信息创建。  

这可以通过在配置 genericSQL 连接器时选中“自动生成”的复选框实现。 选择要自动生成的 DN 时，ECMA 主机将生成 LDAP 格式的 DN：CN=&lt;anchorvalue&gt;,OBJECT=&lt;type&gt;。 这还假定“连接”页中未选中“DN 是定位点”。 
 
 [![未选中“DN 是定位点”](.\media\on-premises-application-provisioning-architecture\user-2.png)](.\media\on-premises-application-provisioning-architecture\user-2.png#lightbox)

genericSQL 连接器要求使用 LDAP 格式填充 DN。  泛型 SQL 连接器使用 LDAP 样式，其组件名称为“OBJECT=”。 这样，它可以使用分区（每种对象类型都是分区）。

由于 ECMA 连接器主机当前仅支持 USER 对象类型，因此 OBJECT=&lt;type&gt; 将为 OBJECT=USER。  于是，anchorvalue 为 ljacobson 的用户的 DN 将是：

  CN=ljacobson,OBJECT=USER


### <a name="user-creation-workflow"></a>用户创建工作流

1.  Azure AD 预配服务会查询 ECMA 连接器主机，以查看用户是否存在。  该服务使用匹配属性作为筛选器。  此属性在 Azure AD 门户中的“企业应用程序”->“本地预配”->“预配”->“属性匹配”下定义。  该属性由 1 表示匹配优先级。
可以定义一种或多种匹配属性并基于优先级设置优先顺序。  若要更改匹配属性，也可以这样做。
 [![匹配属性](.\media\on-premises-application-provisioning-architecture\match-1.png)](.\media\on-premises-application-provisioning-architecture\match-1.png#lightbox)

2.  ECMA 连接器主机接收 GET 请求并查询其内部缓存，以查看用户是否存在并基于导入。  此操作使用查询属性完成。 查询属性在对象类型页中定义。  
 [![查询属性](.\media\on-premises-application-provisioning-architecture\match-2.png)](.\media\on-premises-application-provisioning-architecture\match-2.png#lightbox)


3. 如果用户不存在，Azure AD 将发出 POST 请求来创建用户。  ECMA 连接器主机将使用 HTTP 201 回响 Azure AD，并为用户提供 ID。 此 ID 派生自对象类型页中定义的定位点值。 此定位点由 Azure AD 用于查询 ECMA 连接器主机，以便发出将来和后续的请求。 
4. 如果 Azure AD 中的用户发生更改，Azure AD 将发出 GET 请求，以使用上一步中的定位点（而不是步骤 1 中的匹配属性）检索用户。 例如，这允许更改 UPN，而不会中断该用户在 Azuer AD 中与应用之间的链接。  


## <a name="agent-best-practices"></a>代理最佳做法
- 确保自动 Azure AD Connect 预配代理自动更新服务正在运行。 安装代理时，默认启用此服务。 Microsoft 要求启用自动更新，这样才能为你的部署提供支持。
- 避免对代理与 Azure 之间的出站 TLS 通信进行任何形式的内联检查。 这种内联检查会导致通信流降级。
- 代理必须与 Azure 和应用程序通信，因此，代理的位置会影响这两个连接的延迟时间。 可通过优化每个网络连接来尽量降低端到端流量的延迟。 可通过以下方式优化每个连接：
  - 减少跃点两个端点之间的距离。
  - 选择要遍历的适当网络。 例如，遍历专用网络的速度可能比遍历公共 Internet 速度更快，因为前者是专用链接。



## <a name="provisioning-agent-questions"></a>预配代理问题
下面为一些常见问题提供了解答。

### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>预配代理的正式版本是什么？

有关预配代理的最新正式版本，请参阅 [Azure AD 连接预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)。

### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>如何知道我的预配代理的版本？

 1. 登录到安装有预配代理的 Windows Server。
 2. 转到“控制面板” > “卸载或更改程序”。
 3. 查找与“Microsoft Azure AD Connect 预配代理”条目相对应的版本。

### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft 是否会自动推送预配代理更新？

是的。 如果 Windows 服务“Microsoft Azure AD Connect 代理更新程序”启动并运行，则 Microsoft 会自动更新预配代理。 若要在排查问题时获得支持，需确保代理是最新的。

### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect-or-microsoft-identity-manager"></a>是否可以在运行 Azure AD Connect 或 Microsoft Identity Manager 的同一服务器上安装预配代理？

是的。 可以在运行 Azure AD Connect 或 Microsoft Identity Manager 的同一服务器上安装预配代理，但这不是必需的。

### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>如何将预配代理配置为使用代理服务器来处理出站 HTTP 通信？

预配代理支持使用出站代理。 可通过编辑代理配置文件“C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config”对其进行配置。在文件的末尾添加以下行，恰好添加在尾随的 `</configuration>` 标记前面。 将变量 `[proxy-server]` 和 `[proxy-port]` 替换为代理服务器名称和端口值。

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
### <a name="how-do-i-ensure-the-provisioning-agent-can-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>如何确保预配代理能够与 Azure AD 租户进行通信且防火墙不阻止代理所需的端口？

还可以检查所有必需端口是否已打开。

### <a name="how-do-i-uninstall-the-provisioning-agent"></a>如何卸载预配代理？
 1. 登录到安装有预配代理的 Windows Server。
 2. 转到“控制面板” > “卸载或更改程序”。
 3. 卸载以下程序：
     - Microsoft Azure AD Connect 预配代理
     - Microsoft Azure AD Connect 代理更新程序
     - Microsoft Azure AD Connect 预配代理包





## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
