---
title: 使用 Azure Active Directory 应用程序代理发布远程桌面
description: 介绍如何配置应用程序代理与远程桌面服务 (RDS)
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 07/12/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: aa5c8ae942bb48c78526867eb44cc2084c056d48
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649680"
---
# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理发布远程桌面

结合使用远程桌面服务和 Azure AD 应用程序代理可提高远程办公人员的生产力。 

本文的目标读者为：
- 想要通过远程桌面服务发布本地应用程序，为最终用户提供更多应用程序的当前应用程序代理客户。
- 想要使用 Azure AD 应用程序代理减小其部署的受攻击面的当前远程桌面服务客户。 此访问在 RDS 中提供一组双重验证和条件访问控制机制。

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>应用程序代理如何适应标准 RDS 部署

标准 RDS 部署包括 Windows Server 上运行的各种远程桌面角色服务。 在[远程桌面服务体系结构](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture)中了解多个部署选项。 和其他 RDS 部署选项不同，[使用 Azure AD 应用程序代理的 RDS 部署](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture)（如下图所示）可以从运行连接器服务的服务器提供永久性出站连接。 其他部署通过负载均衡器保留打开的入站连接。

![应用程序代理位于 RDS VM 与公共 Internet 之间](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

在 RDS 部署中，RD Web 角色和 RD 网关角色在面向 Internet 的计算机上运行。 由于以下原因，已公开这些终结点：
- RD Web 为用户提供一个公共终结点用于登录以及查看他们可以访问的各个本地应用程序和桌面。 选择资源后，会在 OS 上使用本机应用创建 RDP 连接。
- 用户启动 RDP 连接后，RD 网关开始发挥作用。 RD 网关处理通过 Internet 传入的加密 RDP 流量，并将其转换到用户要连接的本地服务器。 在此方案中，RD 网关接收的流量来自 Azure AD 应用程序代理。

>[!TIP]
>如果以前尚未部署 RDS，或者在开始之前想要了解详细信息，请了解如何[使用 Azure 资源管理器和 Azure 市场无缝部署 RDS](/windows-server/remote/remote-desktop-services/rds-in-azure)。

## <a name="requirements"></a>要求

- RD Web 和 RD 网关终结点必须位于同一台计算机上，并且有一个共用的根。 RD Web 和 RD 网关将作为具有应用程序代理的单个应用程序发布，因此，可以在两个应用程序之间体验单一登录。
- 应该[已部署 RDS](/windows-server/remote/remote-desktop-services/rds-in-azure) 并[已启用应用程序代理](../app-proxy/application-proxy-add-on-premises-application.md)。 确保已满足启用应用程序代理的先决条件，如安装连接器、打开所需的端口和 URL，以及在服务器上启用 TLS 1.2。 若要了解需要打开哪些端口以及其他详细信息，请参阅[教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)。
- 最终用户必须使用兼容的浏览器才能连接到 RD Web 或 RD Web 客户端。 有关更多详细信息，请参阅[客户端配置支持](#support-for-other-client-configurations)。
- 发布 RD Web 时，建议使用相同的内部和外部 FQDN。 如果内部和外部 FQDN 不同，应禁用请求头转换，以免客户端收到无效链接。
- 如果在 Internet Explorer 上使用 RD Web，则需要启用 RDS ActiveX 加载项。
- 如果使用的是 RD Web 客户端，则需要使用应用程序代理[连接器版本 1.5.1975 或更高版本](./application-proxy-release-version-history.md)。
- 对于 Azure AD 预身份验证流，用户只能连接到“RemoteApp 和桌面”窗格中发布给他们的资源。 用户无法使用“连接到远程电脑”窗格连接到桌面。
- 如果使用的是 Windows Server 2019，可能需要禁用 HTTP2 协议。 有关详细信息，请参阅[教程：在 Azure Active Directory 中添加本地应用程序，以便通过应用程序代理进行远程访问](../app-proxy/application-proxy-add-on-premises-application.md)。

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>部署 RDS 和应用程序代理联合方案

为环境设置 RDS 和 Azure AD 应用程序代理后，请遵循合并两个解决方案的步骤。 这些步骤将会引导你以应用程序的形式发布两个面向 Web 的 RDS 终结点（RD Web 和 RD 网关），并定向 RDS 上的流量，使其流经应用程序代理。

### <a name="publish-the-rd-host-endpoint"></a>发布 RD 主机终结点

1. 使用以下值[发布新的应用程序代理应用程序](../app-proxy/application-proxy-add-on-premises-application.md)：
   - 内部 URL：`https://<rdhost>.com/`，其中，`<rdhost>` 是 RD Web 和 RD 网关共享的共用根。
   - 外部 URL：系统会根据应用程序的名称自动填充此字段，但可以修改它。 用户访问 RDS 时，会转到此 URL。
   - 预身份验证方法：Azure Active Directory
   - 转换 URL 标头：否
2. 将用户分配到已发布的 RD 应用程序。 确保这些用户也都有权访问 RDS。
3. 将应用程序的单一登录方法保留为“已禁用 Azure AD 单一登录”。

   >[!Note]
   >用户必须在 Azure AD 和 RD Web 上各执行身份验证一次，但用户可以单一登录到 RD 网关。

4. 依次选择“Azure Active Directory”和“应用注册” 。 从列表中选择应用。
5. 在“管理”下，选择“品牌打造” 。
6. 更新“主页 URL”字段以指向你的 RD Web 终结点（如 `https://<rdhost>.com/RDWeb`）。

### <a name="direct-rds-traffic-to-application-proxy"></a>将 RDS 流量定向到应用程序代理

以管理员身份连接到 RDS 部署，并更改部署的 RD 网关服务器名称。 此配置可确保连接通过 Azure AD 应用程序代理服务。

1. 连接到运行 RD 连接代理角色的 RDS 服务器。
2. 启动“服务器管理器”。
3. 在左侧窗格中选择“远程桌面服务”。
4. 选择“概述”。 
5. 在“部署概述”部分中，选择下拉菜单并选择“编辑部署属性”。
6. 在“RD 网关”选项卡中，将“服务器名称”字段更改为针对应用程序代理中的 RD 主机终结点设置的外部 URL。
7. 将“登录方法”字段更改为“密码身份验证”。

   ![RDS 上的“部署属性”屏幕](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. 为所有集合运行此命令。 请将 *\<yourcollectionname\>* 和 *\<proxyfrontendurl\>* 替换为自己的信息。 此命令在 RD Web 与 RD 网关之间启用单一登录并优化性能：

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   例如：
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >上面的命令在“`nrequire”中使用反引号。

9. 若要验证自定义 RDP 属性的修改并查看将从此集合的 RDWeb 下载的 RDP 文件内容，请运行以下命令：
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

配置远程桌面后，Azure AD 应用程序代理会充当 RDS.的面向 Internet 的组件。 可以在 RD Web 和 RD 网关计算机上删除其他面向 Internet 的公共终结点。

### <a name="enable-the-rd-web-client"></a>启用 RD Web 客户端
如果你还希望用户能够使用 RD Web 客户端，请按照[为用户设置远程桌面 Web 客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin)中的步骤来启用此操作。

远程桌面 Web 客户端允许用户通过与 HTML5 兼容的 Web 浏览器（如 Microsoft Edge、Internet Explorer 11、Google Chrome、Safari 或 Mozilla Firefox（v55.0 和更高版本））来访问组织的远程桌面基础结构。

## <a name="test-the-scenario"></a>测试方案

在 Windows 7 或 10 计算机上使用 Internet Explorer 测试该方案。

1. 转到设置的外部 URL，或者在 [MyApps 面板](https://myapps.microsoft.com)中找到应用程序。
2. 系统将要求在 Azure Active Directory 中进行身份验证。 请使用分配给应用程序的帐户。
3. 系统将要求在 RD Web 中进行身份验证。
4. RDS 身份验证成功后，可以选择所需的桌面或应用程序，并开始工作。

## <a name="support-for-other-client-configurations"></a>对其他客户端配置的支持

本文中所述的配置适用于通过 RD Web 或 RD Web 客户端访问 RDS。 但是，如果需要，也可以支持其他操作系统或浏览器。 区别在于你使用的身份验证方法会有不同。

| 身份验证方法 | 支持的客户端配置 |
| --------------------- | ------------------------------ |
| 预身份验证    | RD Web-  使用 Internet Explorer* 或 [Edge Chromium IE 模式](/deployedge/edge-ie-mode)和 RDS ActiveX 加载项的 Windows 7/10 |
| 预身份验证    | RD Web 客户端 - 与 HTML5 兼容的 Web 浏览器（如 Microsoft Edge、Internet Explorer 11、Google Chrome、Safari 或 Mozilla Firefox（v55.0 和更高版本）） |
| 传递 | 支持 Microsoft 远程桌面应用程序的任何其他操作系统 |

*当“我的应用”门户用于访问远程桌面应用时，需要 Edge Chromium IE 模式。  

相比传递流，预身份验证流可提供更多的安全优势。 使用预身份验证，你可以将单一登录、条件访问和双重验证等 Azure AD 身份验证功能应用于本地资源。 此外，你还可以确保只有经过身份验证的流量才能访问你的网络。

若要使用传递身份验证，本文列出的步骤仅有下面两处修改：
1. 在[发布 RD 主机终结点](#publish-the-rd-host-endpoint)步骤 1 中，将预身份验证方法设置为“传递”。
2. 在[使 RDS 流量直接流向应用程序代理](#direct-rds-traffic-to-application-proxy)中，完全跳过步骤 8。

## <a name="next-steps"></a>后续步骤
- [通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-integrate-with-sharepoint-server.md)
- [使用 Azure AD 应用程序代理远程访问应用时的安全注意事项](application-proxy-security.md)
- [负载均衡多个应用服务器的最佳做法](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)
