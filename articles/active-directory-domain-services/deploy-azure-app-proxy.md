---
title: 为 Azure AD 域服务部署 Azure AD 应用程序代理 | Microsoft Docs
description: 了解如何通过在 Azure Active Directory 域服务托管域中部署并配置 Azure Active Directory 应用程序代理，为远程工作者提供对内部应用程序的安全访问
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b01595353dd9857409c75ab95271452fb3cee7b0
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228560"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>在 Azure Active Directory 域服务托管域中部署 Azure AD 应用程序代理，以用于安全访问内部应用程序

使用 Azure AD 域服务 (Azure AD DS)，可以将本地运行的旧应用程序直接迁移到 Azure。 然后，Azure Active Directory (AD) 应用程序代理可帮助你为远程工作者提供支持，因为它可以安全发布 Azure AD DS 托管域的这些内部应用程序部分，使其可供通过 Internet 进行访问。

如果你是 Azure AD 应用程序代理的新手并想要了解详细信息，请参阅[如何提供对内部应用程序的安全远程访问](../active-directory/app-proxy/application-proxy.md)。

本文介绍如何创建并配置 Azure AD 应用程序代理连接器，以提供对托管域中应用程序的安全访问。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
    * 需有一个 Azure AD Premium 许可证才能使用 Azure AD 应用程序代理。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。

## <a name="create-a-domain-joined-windows-vm"></a>创建已加入域的 Windows VM

若要将流量路由到环境中运行的应用程序，请安装 Azure AD 应用程序代理连接器组件。 此 Azure AD 应用程序代理连接器必须安装在已加入托管域的 Windows Server 虚拟机 (VM) 上。 对于某些应用程序，可以部署多个服务器并在每个服务器上安装连接器。 此部署选项提供更好的可用性，并可帮助处理较重的身份验证负载。

运行 Azure AD 应用程序代理连接器的 VM 必须位于与托管域所在的同一个虚拟网络中，或者位于与该虚拟网络建立了对等互连的虚拟网络中。 托管你使用应用程序代理发布的应用程序的 VM 也必须部署在同一个 Azure 虚拟网络中。

若要为 Azure AD 应用程序代理连接器创建 VM，请完成以下步骤：

1. [创建自定义 OU](create-ou.md)。 可以将管理此自定义 OU 的权限委托给托管域中的用户。 用于 Azure AD 应用程序代理并运行你的应用程序的 VM 必须是自定义 OU 的一部分，而不是默认“AAD DC 计算机”OU的一部分。
1. [将虚拟机加入托管域][create-join-windows-vm]。这些虚拟机既包括运行 Azure AD 应用程序代理连接器的那个虚拟机，也包括运行你的应用程序的虚拟机。 在上一步的自定义 OU 中创建这些计算机帐户。

## <a name="download-the-azure-ad-application-proxy-connector"></a>下载 Azure AD 应用程序代理连接器

执行以下步骤下载 Azure AD 应用程序代理连接器。 在下一部分，需要将下载的安装程序文件复制到应用代理 VM。

1. 使用一个在 Azure AD 中拥有“企业管理员”权限的用户帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户顶部搜索并选择“Azure Active Directory”，然后选择“企业应用程序” 。
1. 在左侧菜单中选择“应用程序代理”。 若要创建第一个连接器并启用应用代理，请选择“下载连接器”链接。
1. 在下载页上接受许可条款和隐私协议，然后选择“接受条款并下载”。

    ![下载 Azure AD 应用代理连接器](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>安装并注册 Azure AD 应用程序代理连接器

准备好一个可用作 Azure AD 应用程序代理连接器的 VM 后，复制并运行从 Azure 门户下载的安装程序文件。

1. 将 Azure AD 应用程序代理连接器安装程序文件复制到 VM。
1. 运行安装程序文件，例如 AADApplicationProxyConnectorInstaller.exe。 接受软件许可条款。
1. 在安装期间，系统会提示将连接器注册到 Azure AD 目录中的应用程序代理。
   * 提供 Azure AD 目录中全局管理员的凭据。 Azure AD 全局管理员凭据可能与门户中的 Azure 凭据不同

        > [!NOTE]
        > 用于注册连接器的全局管理员帐户必须属于已启用应用程序代理服务的同一目录。
        >
        > 例如，如果 Azure AD 域为 contoso.com，则全局管理员应为 `admin@contoso.com` 或该域上的另一个有效别名。

   * 如果为安装连接器的 VM 启用“Internet Explorer 增强的安全配置”，则可能无法显示注册屏幕。 若要允许访问，请按照错误消息中的说明进行操作，或在安装过程中关闭“Internet Explorer 增强的安全性”。
   * 如果连接器注册失败，请参阅[排查应用程序代理的问题](../active-directory/app-proxy/application-proxy-troubleshoot.md)。
1. 安装结束时，会显示包含出站代理的环境说明。 若要将 Azure AD 应用程序代理连接器配置为通过出站代理进行工作，请运行提供的脚本，例如 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`。
1. 在 Azure 门户中的“应用程序代理”页上，将会列出状态为“活动”的新连接器，如以下示例中所示：

    ![新的 Azure AD 应用程序代理连接器在 Azure 门户中显示为“活动”](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> 若要提高通过 Azure AD 应用程序代理进行身份验证的应用程序的可用性，可在多个 VM 上安装连接器。 重复上一部分中列出的相同步骤，以便在加入到托管域的其他服务器上安装连接器。

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>启用基于资源的 Kerberos 约束委派

如果你要使用集成 Windows 身份验证 (IWA) 实现应用程序的单一登录，请授予 Azure AD 应用程序代理连接器权限来模拟用户并代表用户发送和接收令牌。 若要授予这些权限，请为连接器配置 Kerberos 约束委派 (KCD) 以访问托管域上的资源。 由于你在托管域中没有域管理员特权，因此无法在托管域上配置传统的帐户级 KCD。 请改用基于资源的 KCD。

有关详细信息，请参阅[在 Azure Active Directory 域服务中配置 Kerberos 约束委派 (KCD)](deploy-kcd.md)。

> [!NOTE]
> 必须登录到作为 Azure AD 租户中“Azure AD DC 管理员”组成员的用户帐户才能运行以下 PowerShell cmdlet。
>
> 应用代理连接器 VM 和应用程序 VM 的计算机帐户必须位于你有权配置基于资源的 KCD 的自定义 OU 中。 无法为内置“AAD DC 计算机”容器中的计算机帐户配置基于资源的 KCD。

使用 [Get-ADComputer][Get-ADComputer] 检索要在其上安装 Azure AD 应用程序代理连接器的计算机的设置。 在已加入域的管理 VM 中，使用属于“Azure AD DC 管理员”组成员的用户帐户登录，运行以下 cmdlet。

以下示例获取有关名为 appproxy.aaddscontoso.com 的计算机帐户的信息。 请提供在前面步骤中为 Azure AD 应用程序代理 VM 配置的你自己的计算机名。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

对于在 Azure AD 应用程序代理后面运行应用的每个应用程序服务器，请使用 [Set-ADComputer][Set-ADComputer] PowerShell cmdlet 配置基于资源的 KCD。 在以下示例中，Azure AD 应用程序代理连接器被授予了使用 appserver.aaddscontoso.com 计算机的权限：

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

如果部署多个 Azure AD 应用程序代理连接器，则必须为每个连接器实例配置基于资源的 KCD。

## <a name="next-steps"></a>后续步骤

将 Azure AD 应用程序代理与 Azure AD DS 集成后，发布应用程序供用户访问。 有关详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](../active-directory/app-proxy/application-proxy-add-on-premises-application.md)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/activedirectory/get-adcomputer
[Set-ADComputer]: /powershell/module/activedirectory/set-adcomputer