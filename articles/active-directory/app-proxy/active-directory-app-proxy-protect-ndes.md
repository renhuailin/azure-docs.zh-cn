---
title: 与 NDES 服务器上的 Azure Active Directory 应用程序代理集成
description: 有关部署 Azure Active Directory 应用程序代理以保护 NDES 服务器的指南。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.openlocfilehash: 3c55bdb003f16539061bcbeae0f8cf7ed5cc660d
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728275"
---
# <a name="integrate-with-azure-active-directory-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>与网络设备注册服务 (NDES) 服务器上的 Azure Active Directory 应用程序代理集成

通过 Azure Active Directory (AD) 应用程序代理，可在你的网络中发布应用程序。 这些应用程序包括 SharePoint 站点、Microsoft Outlook Web App 和其他 Web 应用程序。 还可通过 Azure 向网络外的用户提供安全访问。

如果你不熟悉 Azure AD 应用程序代理，希望了解详细信息，请参阅[通过 Azure AD 应用程序代理远程访问本地应用程序](application-proxy.md)。

Azure AD 应用程序代理构建在 Azure 上。 该代理提供了大量网络带宽和服务器基础设施，以便更好地防范分布式拒绝服务 (DDOS) 攻击，以及提高代理的可用性。 而且，无需向本地网络开放外部防火墙端口，也不需要任何 DMZ 服务器。 所有流量均来自入站。 有关出站端口的完整列表，请参阅[教程：通过 Azure Active Directory 应用程序代理添加远程访问的本地应用程序](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)。

> 只有在使用高级版或基础版 Azure Active Directory 的前提下，才可使用 Azure AD 应用程序代理这一功能。 有关详细信息，请参阅 [Azure Active Directory 定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。 
> 如果你拥有企业移动性套件 (EMS) 许可证，则可以使用此解决方案。
> Azure AD 应用程序代理连接器仅安装在 Windows Server 2012 R2 或更高版本上。 这也是 NDES 服务器的要求。

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>在 NDES 服务器上安装并注册连接器

1. 以使用应用程序代理的目录的应用程序管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 例如，如果租户域为 contoso.com，则管理员应为 admin@contoso.com 或该域上的其他任何域别名。
1. 请在右上角选择用户名。 验证是否已登录到使用应用程序代理的目录。 如果需要更改目录，请选择“切换目录”，然后选择使用应用程序代理的目录  。
1. 在左侧导航面板中选择“Azure Active Directory”  。
1. 在“管理”下选择“应用程序代理”   。
1. 选择“下载连接器服务”  。

    ![下载连接器服务，以查看服务条款](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 阅读“服务条款”。 准备就绪后，选择“接受条款并下载”  。
1. 将 Azure AD 应用程序代理连接器安装程序文件复制到 NDES 服务器。 
   > 可将连接器安装在企业网络中任何可以访问 NDES 的服务器上。 不需要在 NDES 服务器上安装连接器。
1. 运行安装程序文件，如 AADApplicationProxyConnectorInstaller.exe。 接受软件许可条款。
1. 在安装期间，系统会提示将连接器注册到 Azure AD 目录的应用程序代理。
   * 提供 Azure AD 目录中全局管理员或应用程序管理员凭据。 Azure AD 全局或应用程序管理员凭证可能与门户中的 Azure 凭证不同。

        > [!NOTE]
        > 用于注册连接器的全局或应用管理员帐户必须属于启用应用代理服务的同一目录。
        >
        > 例如，如果 Azure AD 域为 contoso.com，则全局/应用程序管理员应为 `admin@contoso.com` 或该域上另一个有效别名。

   * 如果为安装连接器的服务器启用“Internet Explorer 增强的安全配置”，则可能无法显示注册屏幕。 要显示该屏幕，请按照错误消息中的说明进行操作，或在安装过程中关闭“Internet Explorer 增强的安全性”设置。
   * 如果连接器注册失败，请参阅[排查应用程序代理的问题](application-proxy-troubleshoot.md)。
1. 安装结束时，会显示包含出站代理的环境说明。 若要将 Azure AD 应用程序代理连接器配置为通过出站代理进行工作，请运行提供的脚本，例如 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`。
1. 在 Azure 门户中的“应用程序代理”页上，将会列出状态为“活动”的新连接器，如以下示例中所示：

    ![新的 Azure AD 应用程序代理连接器在 Azure 门户中显示为“活动”](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > 若要提高通过 Azure AD 应用程序代理进行身份验证的应用程序的可用性，可在多个 VM 上安装连接器。 重复上一部分中列出的相同步骤，在加入到 Azure AD DS 托管域的其他服务器上安装连接器。

1. 安装成功后，返回到 Azure 门户。

1. 选择“企业应用程序”。

   ![确保选择正确的利益干系人](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. 选择“+ 新建应用程序”，然后选择“本地应用程序” 。 

1. 在“添加自己的本地应用程序”中，配置下列字段：

   * **名称**：输入应用程序的名称。
   * **内部 URL**：输入 NDES 服务器的内部 URL/FQDN，在该服务器上安装连接器。
   * **预身份验证**：选择“传递”。 不能使用任何形式的预身份验证。 用于证书请求 (SCEP) 的协议不提供此类选项。
   * 复制提供的“外部 URL”。

1. 选择“+ 添加”，保存应用程序。

1. 将步骤 15 中复制的链接粘贴到浏览器中，测试是否可以通过 Azure AD 应用程序代理访问 NDES 服务器。 应会看到一个默认的 IIS 欢迎页。

1. 作为最终测试，将“mscep.dll”路径添加到在上一步中粘贴的现有 URL：

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. 应会看到“HTTP 错误 403 - 禁止访问”响应。

1. 将提供的 NDES URL（通过 Microsoft Intune）更改为设备。 可在 Microsoft Endpoint Configuration Manager 或 Microsoft Endpoint Manager 管理中心进行此更改。

   * 对于 Configuration Manager，请前往证书注册点并调整 URL。 此 URL 是设备发出的内容，提示设备遇到的难题。
   * 对于 Intune 独立版，可以编辑或创建新的 SCEP 策略并添加新的 URL。

## <a name="next-steps"></a>后续步骤

将 Azure AD 应用程序代理与 NDES 集成后，发布用户要访问的应用程序。 有关详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](./application-proxy-add-on-premises-application.md)。
