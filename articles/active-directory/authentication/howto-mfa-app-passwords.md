---
title: 在 Azure AD 多重身份验证中配置应用密码 - Azure Active Directory
description: 了解如何在 Azure AD 多重身份验证中为旧版应用程序配置和使用应用密码
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58344a385a8ad4d409748c039f8f1e7807e290b7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228372"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>使用应用密码对旧版应用程序启用和使用 Azure AD 多重身份验证

某些较旧的非浏览器应用（如 Office 2010 或更早版本以及 iOS 11 之前的 Apple Mail）不理解身份验证过程中的暂停或中断环节。 如果为某个用户启用了 Azure AD 多重身份验证，且该用户尝试使用某个较旧的非浏览器应用，则他们无法成功进行身份验证。 在为用户帐户启用了 Azure AD 多重身份验证的情况下，若要以安全方式使用这些应用程序，可以使用应用密码。 这些应用密码取代了传统密码，允许应用绕过多重身份验证并正常工作。

Microsoft Office 2013 客户端及更高版本支持现代身份验证。 Office 2013 客户端（包括 Outlook）支持新式身份验证协议，并且支持使用双重验证。 启用客户端后，客户端即不要求使用应用密码。

本文介绍如何为不支持多重身份验证提示的旧版应用程序启用和使用应用密码。

>[!NOTE]
> 应用密码不适用于基于条件访问的多重身份验证策略和新式身份验证。

## <a name="overview-and-considerations"></a>概述和注意事项

当用户帐户进行 Azure AD 多重身份验证时，附加验证的请求将中断常规登录提示。 某些较旧的应用程序不理解登录过程中的这种中断，因此身份验证失败。 若要维护用户帐户安全并启用 Azure AD 多重身份验证，可以使用应用密码，而不是用户的常规用户名和密码。 如果在登录过程中使用了应用密码，则没有额外的验证提示，因此身份验证成功。

应用密码是自动生成的，不由用户指定。 自动生成的密码使攻击者更难猜到，因而更安全。 用户不需要记录这些密码，也不用每次都输入它们，因为每个应用程序只需输入一次应用密码。

使用应用密码时需要注意以下事项：

* 每个用户的应用密码限制为 40 个。
* 缓存密码并在本地使用它们的应用程序可能会失败，因为应用密码在工作或学校帐户外并非已知。 这种情况的一个例子是 Exchange 电子邮件位于本地而存档邮件位于云中。 在这种情况下，不能使用同一密码。
* 对用户帐户启用 Azure AD 多重身份验证后，应用密码就可用于大多数非浏览器客户端，例如 Outlook 和 Microsoft Skype for Business。 但是，不能通过非浏览器应用程序（如 Windows PowerShell）使用应用密码执行管理操作。 即使用户具有管理帐户，也无法执行这些操作。
    * 若要运行 PowerShell 交吧，应创建具有强密码的服务帐户，并且不为该帐户启用双重验证。
* 如果怀疑某个用户帐户已泄露并撤消/重置了帐户密码，则应用密码也应该更新。 撤消/重置用户帐户密码时，应用密码不会自动撤销。 用户应删除现有的应用密码，然后创建新的应用密码。
   * 有关详细信息，请参阅[在“其他安全验证”页中创建和删除应用密码](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)。

>[!WARNING]
> 在客户端同时与本地和云自动发现终结点通信的混合环境中，应用密码无效。 需要域密码才能进行本地身份验证。 需要应用密码才能进行云身份验证。

### <a name="app-password-names"></a>应用密码名称

应用密码名称应能反映使用它们的设备。 如果有安装了非浏览器应用（如 Outlook、Word 和 Excel）的笔记本电脑，可为这些应用创建名为 Laptop 的应用密码。 为在台式计算机上运行的相同应用程序创建名为 Desktop 的另一个应用密码。

建议为每个设备（而不是每个应用程序）创建一个应用密码。

## <a name="federated-or-single-sign-on-app-passwords"></a>联合或单一登录应用密码

Azure AD 支持与本地 Active Directory 域服务 (AD DS) 联合（也称单一登录 (SSO)）。 如果组织与 Azure AD 联合，并且你正在使用 Azure AD 多重身份验证，请考虑以下应用密码注意事项：

>[!NOTE]
> 以下要点仅适用于联合 (SSO) 客户。

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会主动使用联合。
* 与被动流程不同，对于联合 (SSO) 用户，不会与标识提供者 (IdP) 联系。 应用密码存储在工作或学校帐户中。 如果用户离开公司，则该用户的信息通过 **DirSync** 实时流向工作或学校帐户。 禁用/删除帐户可能需要长达三小时才能同步，这可能使 Azure AD 中的应用密码的禁用/删除出现延迟。
* 应用密码功能不遵循“本地客户端访问控制”设置。
* 没有可与应用密码功能同时使用的本地身份验证日志记录或审核功能。

某些高级体系结构要求结合使用凭据进行客户端多重身份验证。 凭据可以包括工作或学校帐户的用户名和密码以及应用密码。 要求取决于如何执行身份验证。 对于针对本地基础结构进行身份验证的客户端，需要使用工作或学校帐户用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，需要使用应用密码。

例如，假设有以下体系结构：

* Active Directory 的本地实例与 Azure AD 联合。
* 使用 Exchange Online。
* 在本地使用 Skype for Business。
* 使用 Azure AD 多重身份验证。

在此方案中，使用以下凭据：

* 若要登录到 Skype for Business，请使用工作或学校帐户的用户名和密码。
* 若要从连接到 Exchange Online 的 Outlook 客户端访问通讯簿，请使用应用密码。

## <a name="allow-users-to-create-app-passwords"></a>允许用户创建应用密码

默认情况下，用户无法创建应用密码。 必须先启用应用密码功能，用户才能使用它们。 为了让用户能够创建应用密码，管理员需要完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 搜索并选择“Azure Active Directory”，然后选择“用户”。 
3. 在“用户”窗口顶部的导航栏中，选择“多重身份验证”。
4. 在“多重身份验证”下，选择“服务设置”。
5. 在“服务设置”页上，选择“允许用户创建应用密码，以登录非浏览器应用”选项。 

    ![Azure 门户的屏幕截图，其中显示了允许用户使用应用密码进行多重身份验证的服务设置](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> 禁用用户创建应用密码的功能后，现有应用密码将继续有效。 但是，禁用此功能后，用户无法管理或删除这些现有应用密码。
>
> 禁用创建应用密码的功能时，还建议[创建条件访问策略以禁用旧式身份验证](../conditional-access/block-legacy-authentication.md)。 此方法可防止现有应用密码正常工作，并强制使用新式身份验证方法。

## <a name="create-an-app-password"></a>创建应用密码

当用户完成 Azure AD 多重身份验证的初始注册后，可以选择在注册过程结束时创建应用密码。

用户还可以在注册后创建应用密码。 有关用户的更多信息和详细步骤，请参阅以下资源：
* [Azure AD 多重身份验证中的应用密码是什么？](../user-help/multi-factor-authentication-end-user-app-passwords.md)
* [从“安全信息”页创建应用密码](../user-help/security-info-app-passwords.md)

## <a name="next-steps"></a>后续步骤

若要详细了解如何允许用户快速注册 Azure AD 多重身份验证，请参阅[组合的安全信息注册概述](concept-registration-mfa-sspr-combined.md)。