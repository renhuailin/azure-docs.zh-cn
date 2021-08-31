---
title: 自定义 Azure Active Directory Connect 的安装
description: 本文介绍 Azure AD Connect 的自定义安装选项。 使用本文中的说明来通过 Azure AD Connect 安装 Active Directory。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29cc31121a4888c23ccbec1c549f2313d0c9e165
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439315"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect 的自定义安装
如果希望有更多的安装选项，请使用 Azure Active Directory (Azure AD) Connect 中的自定义设置。 例如，如果你有多个林或希望配置可选功能，请使用这些设置。 只要[快速安装](how-to-connect-install-express.md)不能满足部署或拓扑需求，即可使用自定义设置。

先决条件：
- [下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)。
- 完成 [Azure AD Connect：硬件和先决条件](how-to-connect-install-prerequisites.md)中的先决条件步骤。 
- 请确保拥有 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md)中所述的帐户。

## <a name="custom-installation-settings"></a>自定义安装设置 

若要为 Azure AD Connect 设置自定义安装，请完成以下部分所述的向导页面。

### <a name="express-settings"></a>快速设置
在“快速设置”页面上选择“自定义”，开始自定义设置安装。   本文的其余部分将指导你完成自定义安装过程。 使用以下链接可快速访问特定页面的信息：

- [所需的组件](#install-required-components)
- [用户登录](#user-sign-in)
- [连接到 Azure AD](#connect-to-azure-ad)
- [同步](#sync-pages)

### <a name="install-required-components"></a>安装所需的组件
安装同步服务时，可以让可选配置部分保持未选中状态， Azure AD Connect 会自动完成所有设置。 它会设置 SQL Server 2019 Express LocalDB 实例、创建相应的组并分配权限。 如果要更改默认值，请取消选中相应的框。  下表汇总了这些选项，并提供指向其他信息的链接。 

![屏幕截图，显示 Azure AD Connect 中所需安装组件的可选选项。](./media/how-to-connect-install-custom/requiredcomponents2.png)

| 可选配置 | 说明 |
| --- | --- |
|指定自定义安装位置| 允许你更改 Azure AD Connect 的默认安装路径。|
| 使用现有的 SQL Server |允许你指定 SQL Server 名称和实例名称。 如果已有一个要使用的数据库服务器，请选择此选项。 如果 SQL Server 实例没有启用浏览功能，请在“实例名称”中输入实例名称、逗号和端口号。  然后指定 Azure AD Connect 数据库的名称。  你的 SQL 权限决定了是可以创建新数据库，还是必须由 SQL 管理员提前创建数据库。  如果你有 SQL Server 管理员 (SA) 权限，请参阅[使用现有数据库安装 Azure AD Connect](how-to-connect-install-existing-database.md)。  如果你有委派的权限 (DBO)，请参阅[使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)。 |
| 使用现有的服务帐户 |默认情况下，Azure AD Connect 提供的虚拟服务帐户用于同步服务。 如果使用远程 SQL Server 实例或使用需要身份验证的代理，可以使用托管服务帐户或域中受密码保护的服务帐户。 在这些情况下，请输入要使用的帐户。 若要运行安装，你必须是 SQL 中的 SA，这样你才能创建服务帐户的登录凭据。 有关详细信息，请参阅 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md#adsync-service-account)。 </br></br>通过使用最新的内部版本，SQL 管理员现在可以对数据库进行带外预配。 然后，Azure AD Connect 管理员可以使用数据库所有者权限来安装它。  有关详细信息，请参阅[使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)。|
| 指定自定义同步组 |默认情况下，在安装同步服务时，Azure AD Connect 会创建四个位于服务器本地的组。 这些组是：管理员组、操作员组、浏览组和密码重置组。 在此可以指定自己的组。 这些组必须位于服务器本地。 它们不能位于域中。 |
|导入同步设置（预览）|允许你从 Azure AD Connect 的其他版本导入设置。  有关详细信息，请参阅[导入和导出 Azure AD Connect 配置设置](how-to-connect-import-export-config.md)。|

### <a name="user-sign-in"></a>用户登录
安装所需的组件后，选择用户单一登录方法。 下表简要介绍了可用的选项。 有关登录方法的完整说明，请参阅[用户登录](plan-connect-user-signin.md)。

![显示“用户登录”页的屏幕截图。 已选择“密码哈希同步”选项。](./media/how-to-connect-install-custom/usersignin4.png)

| 单一登录选项 | 说明 |
| --- | --- |
| 密码哈希同步 |用户可以用在其本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。 用户密码作为密码哈希同步到 Azure AD。 在云中进行身份验证。 有关详细信息，请参阅[密码哈希同步](how-to-connect-password-hash-synchronization.md)。 |
|直通身份验证|用户可以用在其本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。  用户密码会传递到本地 Active Directory 域控制器进行验证。
| 使用 AD FS 进行联合身份验证 |用户可以用在其本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。  用户被重定向到其本地 Azure Directory 联合身份验证服务 (AD FS) 实例进行登录。 身份验证在本地进行。 |
| 使用 PingFederate 进行联合身份验证|用户可以用在其本地网络中使用的相同密码登录到 Microsoft 云服务，例如 Microsoft 365。  用户被重定向到其本地 PingFederate 实例以进行登录。 身份验证在本地进行。 |
| 不配置 |不安装或配置用户登录功能。 如果已有第三方联合服务器或部署了另一个解决方案，请选择此选项。 |
|启用单一登录|密码哈希同步和直通身份验证均提供此选项。 它为企业网络中的桌面用户提供单一登录体验。 有关详细信息，请参阅[单一登录](how-to-connect-sso.md)。 </br></br>**注意：** 对于 AD FS 客户，此选项不可用。 AD FS 已提供相同级别的单一登录。</br>

### <a name="connect-to-azure-ad"></a>连接到 Azure AD
在“连接到 Azure AD”页上，输入全局管理员帐户和密码。 如果在前一个页面上选择了“使用 AD FS 进行联合身份验证”，则不要使用你计划启用联合身份验证的域中的帐户登录。 

你可能想使用随附于 Azure AD 租户的默认 onmicrosoft.com 域中的帐户。 此帐户只用于在 Azure AD 中创建服务帐户， 安装完成后不会使用。
  
![显示“连接到 Azure AD”页的屏幕截图。](./media/how-to-connect-install-custom/connectaad.png)

如果全局管理员帐户已启用多重身份验证，请在登录窗口中再次提供密码，并且必须完成多重身份验证质询。 该质询可能是验证码或电话通话。  

![显示“连接到 Azure AD”页的屏幕截图。 多重身份验证字段会提示用户输入代码。](./media/how-to-connect-install-custom/connectaadmfa.png)

全局管理员帐户也可以启用 [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)。

如果出现错误或连接性问题，请参阅[排查连接性问题](tshoot-connect-connectivity.md)。

## <a name="sync-pages"></a>“同步”页面

以下部分介绍“同步”部分中的页面。

### <a name="connect-your-directories"></a>连接目录
若要连接到 Active Directory 域服务 (Azure AD DS)，Azure AD Connect 需要使用具有足够权限的帐户的林名称和凭据。

![显示“连接目录”页的屏幕截图。](./media/how-to-connect-install-custom/connectdir01.png)

输入林名称并选择“添加目录”后，会显示一个窗口。 下表对选项进行了说明。

| 选项 | 说明 |
| --- | --- |
| 创建新帐户 | 创建 Azure AD DS 帐户，Azure AD Connect 在目录同步期间需要使用该帐户来连接到 Active Directory 林。 选择此选项后，请输入企业管理员帐户的用户名和密码。  Azure AD Connect 使用提供的企业管理员帐户创建所需的 Azure AD DS 帐户。 可以采用 NetBIOS 格式或 FQDN 格式输入域部分。 即，输入 FABRIKAM\administrator 或 fabrikam.com\administrator。 |
| 使用现有帐户 | 提供现有的 Azure AD DS 帐户，Azure AD Connect 在目录同步期间可以使用该帐户来连接到 Active Directory 林。 可以采用 NetBIOS 格式或 FQDN 格式输入域部分。 即，输入 FABRIKAM\syncuser 或 fabrikam.com\syncuser。 此帐户可以是普通的用户帐户，因为它只需默认的读取权限。 但是，你可能会需要更多权限，具体取决于你的情况。 有关详细信息，请参阅 [Azure AD Connect 帐户和权限](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)。 |

![屏幕截图，显示了“连接目录”页和 AD 林帐户窗口，你可以在其中选择创建新帐户或使用现有帐户。](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> 从内部版本 1.4.18.0 起，你不再能够使用企业管理员或域管理员帐户作为 Azure AD DS 连接器帐户。 选择“使用现有帐户”时，如果尝试输入企业管理员帐户或域管理员帐户，则会看到以下错误：“不允许对 AD 林帐户使用企业或域管理员帐户。 让 Azure AD Connect 为你创建帐户，或者指定一个具有适当权限的同步帐户。”
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD 登录配置
在“Azure AD 登录配置”页上，查看本地 Azure AD DS 中的用户主体名称 (UPN) 域。 这些 UPN 域已在 Azure AD 中进行了验证。 在此页上，请配置要用于 userPrincipalName 的属性。

![屏幕截图，显示“Azure AD 登录配置”页上未验证的域。](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

查看标记为“未添加”或“未验证”的每个域。  确保使用的域都已在 Azure AD 中经过了验证。 验证域后，选择“循环刷新”图标。 有关详细信息，请参阅[添加和验证域](../fundamentals/add-custom-domain.md)。

用户在登录到 Azure AD 和 Microsoft 365 时使用 userPrincipalName 属性。 Azure AD 应在对用户进行同步之前验证域（也称为 UPN 后缀）。 Microsoft 建议你保留默认属性 userPrincipalName。 

如果 userPrincipalName 属性不可路由且无法进行验证，可以选择另一属性。 例如，可以选择 email 作为保存登录 ID 的属性。 使用除 userPrincipalName 以外的某个属性时，该属性称为“备用 ID”。 

备用 ID 属性值必须遵循 RFC 822 标准。 可以将替代 ID 与密码哈希同步、直通身份验证和联合身份验证一起使用。 在 Active Directory 中，不能将该属性定义为多值，即使它只有单个值。 有关替代 ID 的详细信息，请参阅[直通身份验证：常见问题](./how-to-connect-pta-faq.yml#does-pass-through-authentication-support--alternate-id--as-the-username--instead-of--userprincipalname--)。

>[!NOTE]
> 启用直通身份验证时，必须至少有一个已验证的域才能继续自定义安装过程。

> [!WARNING]
> 备用 ID 不与所有 Microsoft 365 工作负荷兼容。 有关详细信息，请参阅[配置备用登录 ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。
>

### <a name="domain-and-ou-filtering"></a>域和 OU 筛选
默认情况下会同步所有域和组织单位 (OU)。 如果不想将某些域或 OU 同步到 Azure AD，可以取消选中相应的选项。  

![屏幕截图，显示“域和 OU 筛选”页。](./media/how-to-connect-install-custom/domainoufiltering.png)  

此页配置基于域和基于 OU 的筛选。 如果打算进行更改，请参阅[基于域的筛选](how-to-connect-sync-configure-filtering.md#domain-based-filtering)和[基于 OU 的筛选](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。 某些 OU 对功能至关重要，应让其处于选中状态。

如果将基于 OU 的筛选与早于 1.1.524.0 的 Azure AD Connect 版本配合使用，则会默认同步新 OU。 如果不想要同步新 OU，则可以在[基于 OU 的筛选](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)步骤之后调整默认行为。 对于 Azure AD Connect 1.1.524.0 或更高版本，可以指示是否需要同步新 OU。

如果打算使用[基于组的筛选](#sync-filtering-based-on-groups)，请确保包含该组所在的 OU，而未使用 OU 筛选将该 OU 筛选掉。 OU 筛选是在基于组的筛选被评估之前评估的。

由于防火墙限制，也可能无法连接到某些域。 这些域在默认情况下处于未选中状态，并且它们会显示警告。  

![屏幕截图，显示无法访问的域。](./media/how-to-connect-install-custom/unreachable.png)  

如果看到此警告，请确认这些域确实无法访问，并且该警报在意料之中。

### <a name="uniquely-identifying-your-users"></a>唯一标识用户

在“标识用户”页上，选择如何在本地目录中标识用户，以及如何使用 sourceAnchor 属性来标识他们。

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>选择应如何在本地目录中标识用户
使用“跨林匹配”功能，可以定义如何在 Azure AD 中呈现 Azure AD DS 林中的用户。 一个用户可能会在所有林中只被表示一次，也可能会具有已启用和已禁用帐户的组合。 在某些林中，用户还可以被呈现为联系人。

![屏幕截图，显示可在其中对用户进行唯一标识的页面。](./media/how-to-connect-install-custom/unique2.png)

| 设置 | 说明 |
| --- | --- |
| [用户在所有林中只被表示一次](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |将所有用户在 Azure AD 中创建为单独的对象。 不会在 Metaverse 中联接对象。 |
| [邮件属性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |如果邮件属性在不同的林中具有相同的值，此选项将联接用户和联系人。 当已使用 GALSync 创建了联系人时，请使用此选项。 如果选择此选项，则不会将 mail 属性未被填充的用户对象同步到 Azure AD。 |
| [ObjectSID 和 msExchangeMasterAccountSID/ msRTCSIP-OriginatorSID 属性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |此选项将帐户林中的已启用用户与资源林中的已禁用用户进行联接。 在 Exchange 中，此配置称为链接邮箱。 如果只使用 Lync 且资源林中没有 Exchange，则可以使用此选项。 |
| SAMAccountName 和 MailNickName 属性 |此选项根据预期可以在其中找到用户登录 ID 的属性进行联接。 |
| 选择特定属性 |此选项允许选择自己的属性。 如果选择此选项，则不会将其（选定）属性未填充的用户对象同步到 Azure AD。 **限制：** 仅 Metaverse 中已有的属性适用此选项。 |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>选择应如何使用源定位点来标识用户
sourceAnchor 属性是一个在用户对象的生命周期内不会改变的属性。 它是将本地用户与 Azure AD 中用户进行关联的主键。

| 设置 | 说明 |
| --- | --- |
| 允许 Azure 管理源定位点 | 如果希望 Azure AD 为你选取属性，请选择此选项。 如果选择此选项，Azure AD Connect 会应用 sourceAnchor 属性选择逻辑，该逻辑在[使用 ms-DS-ConsistencyGuid 作为 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 中进行了说明。 自定义安装完成后，你会看到已选取哪个属性作为 sourceAnchor 属性。 |
| 选择特定属性 | 如果希望指定现有的 AD 属性作为 sourceAnchor 属性，请选择此选项。 |

由于 sourceAnchor 属性无法更改，因此必须选择适当的属性。 objectGUID 就是不错的候选项。 除非在林或域之间移动用户帐户，否则此属性不会更改。 请勿选择某人结婚或更改分配时会改变的属性。 

不能使用包含 at 符号 (@) 的属性，因此不能使用 email 和 userPrincipalName。 属性也区分大小写，因此在林间移动对象时，请务必保留大小写。 二进制属性采用 Base64 编码，但其他属性类型会保留其未编码的状态。 

在联合方案和某些 Azure AD 接口中，sourceAnchor 属性也称为 immutableID。 

有关源定位点的详细信息，请参阅[设计概念](plan-connect-design-concepts.md#sourceanchor)。

### <a name="sync-filtering-based-on-groups"></a>根据组同步筛选
使用按组筛选功能可以只同步一小部分的对象来进行试点。 若要使用此功能，请在 Active Directory 本地实例中针对此目的创建一个组。 然后添加应该以直属成员身份与 Azure AD 同步的用户和组。 稍后可以在此组中添加或删除用户，以维护应该要在 Azure AD 中显示的对象列表。 

要同步的所有对象必须是组的直接成员。 用户、组、联系人和计算机或设备都必须是直接成员。 系统不会解析嵌套组成员身份。 添加组作为成员时，只会添加组本身， 而不添加其成员。

![屏幕截图，显示了一个页面，你可以在其中选择筛选用户和设备的方式。](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> 此功能仅用于支持试点部署。 请不要将其用于完整的生产部署。
>

在完整的生产部署中，很难维护单个组及其要同步的所有对象。 请使用[配置筛选](how-to-connect-sync-configure-filtering.md)中所述的方法之一，而不是按组筛选功能。

### <a name="optional-features"></a>可选功能
在下一页上，可以针对方案选择可选功能。

>[!WARNING]
>Azure AD Connect 1.0.8641.0 及更低版本依赖于 Azure 访问控制服务进行密码写回。  该服务已于 2018 年 11 月 7 日停用。  如果你使用上述任一版本的 Azure AD Connect 并启用了密码写回，则当服务停用后，用户可能无法更改或重置其密码。 这些版本的 Azure AD Connect 不支持密码写回。
>
>有关详细信息，请参阅[从 Azure 访问控制服务迁移](../azuread-dev/active-directory-acs-migration.md)。
>
>如果要使用密码写回，请下载[最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

![屏幕截图，显示“可选功能”页面。](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> 如果 Azure AD Sync 或 Direct Synchronization (DirSync) 处于活动状态，请勿激活 Azure AD Connect 中的任何写回功能。



| 可选功能 | 说明 |
| --- | --- |
| Exchange 混合部署 |Exchange 混合部署功能使 Exchange 邮箱能够在本地和 Microsoft 365 中共存。 Azure AD Connect 会将特定的[属性](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)集从 Azure AD 同步回本地目录。 |
| Exchange 邮件公用文件夹 | “Exchange 邮件公用文件夹”功能可以将支持邮件功能的公用文件夹对象从 Active Directory 的本地实例同步到 Azure AD。 |
| Azure AD 应用程序和属性筛选 |通过启用 Azure AD 应用和属性筛选，可以定制已同步属性的集合。 此选项会在向导中额外添加两个配置页。 有关详细信息，请参阅 [Azure AD 应用程序和属性筛选](#azure-ad-app-and-attribute-filtering)。 |
| 密码哈希同步 |如果选择了联合身份验证作为登录解决方案，则可启用密码哈希同步。 然后，可以将它用作备份选项。  </br></br>如果选择了“直通身份验证”，则可以启用此选项来确保支持旧客户端并提供备份。</br></br> 有关详细信息，请参阅[密码哈希同步](how-to-connect-password-hash-synchronization.md)。|
| 密码写回 |使用此选项来确保源自 Azure AD 的密码更改会写回到本地目录。 有关详细信息，请参阅[密码管理入门](../authentication/tutorial-enable-sspr.md)。 |
| 组写回 |如果使用 Microsoft 365 组，则可以在 Active Directory 的本地实例中表示组。 仅当在 Active Directory 的本地实例中有 Exchange 时，此选项才可用。 有关详细信息，请参阅 [Azure AD Connect 组写回](how-to-connect-group-writeback.md)。|
| 设备写回 |对于条件访问方案，使用此选项可以将 Azure AD 中的设备对象写回 Active Directory 的本地实例中。 有关详细信息，请参阅[在 Azure AD Connect 中启用设备写回](how-to-connect-device-writeback.md)。 |
| 目录扩展属性同步 |选择此选项可将指定的属性同步到 Azure AD。 有关详细信息，请参阅[目录扩展](how-to-connect-sync-feature-directory-extensions.md)。 |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD 应用程序和属性筛选
如果想要限制同步到 Azure AD 的属性，则可以从选择使用的服务来着手。 如果更改此页上的选项，则必须通过重新运行安装向导来显式选择新的服务。

![屏幕截图，显示可选的 Azure AD 应用功能。](./media/how-to-connect-install-custom/azureadapps2.png)

此页面根据上一步选择的服务来显示同步的所有属性。 此列表是要同步的所有对象类型的组合。 如果需要某些属性保持非同步状态，可以从这些属性中清除所选内容。

![屏幕截图，显示可选的 Azure AD 属性特性。](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> 删除属性可能会影响功能。 有关最佳做法和建议，请参阅[要同步的属性](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)。
>

### <a name="directory-extension-attribute-sync"></a>目录扩展属性同步
可以使用组织添加的自定义属性或使用 Active Directory 中的其他属性，在 Azure AD 中扩展架构。 若要使用这项功能，请在“可选功能”页上选择“目录扩展属性同步”。 可以在“目录扩展”页上选择要同步的其他属性。

>[!NOTE]
>“可用属性”字段区分大小写。

![屏幕截图，显示“目录扩展”页。](./media/how-to-connect-install-custom/extension2.png)

有关详细信息，请参阅[目录扩展](how-to-connect-sync-feature-directory-extensions.md)。

### <a name="enabling-single-sign-on"></a>启用单一登录
在“单一登录”页上，可以配置单一登录用于密码同步或直通身份验证。 为每个要同步到 Azure AD 的林执行一次此步骤。 配置包括两个步骤：

1.  在 Active Directory 的本地实例中创建所需的计算机帐户。
2.  配置客户端计算机的 Intranet 区域，使其支持单一登录。

#### <a name="create-the-computer-account-in-active-directory"></a>在 Active Directory 中创建计算机帐户
对于在 Azure AD Connect 中添加的每个林，需要提供域管理员凭据，以便在每个林中创建计算机帐户。 凭据仅用于创建帐户。 它们不存储或用于任何其他操作。 在“启用单一登录”页上添加凭据，如下图所示。

![显示“启用单一登录”页的屏幕截图。 添加林凭据。](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>可以跳过不想使用单一登录的林。

#### <a name="configure-the-intranet-zone-for-client-machines"></a>配置客户端计算机的 Intranet 区域
为了确保客户端在 Intranet 区域中自动登录，需确保该 URL 是 Intranet 区域的一部分。 此步骤可以确保已加入域的计算机在连接到企业网络后，向 Azure AD 自动发送 Kerberos 票证。

在装有组策略管理工具的计算机上：

1.  打开组策略管理工具。
2.  编辑要应用到所有用户的组策略。 例如默认域策略。
3.  转到“用户配置” > “管理模板” > “Windows 组件” > “Internet Explorer” > “Internet 控制面板” > “安全页”。 然后选择“站点到区域分配列表”。
4.  启用策略。 然后，在对话框中，输入值名称 `https://autologon.microsoftazuread-sso.com` 和值 `1`。 设置应如下图所示。
  
    ![显示 Intranet 区域的屏幕截图。](./media/how-to-connect-install-custom/sitezone.png)

6.  选择“确定”两次。

## <a name="configuring-federation-with-ad-fs"></a>配置与 AD FS 的联合
只需单击几下鼠标，即可使用 Azure AD Connect 配置 AD FS。 在开始之前，你需要：

* 用于联合服务器的 Windows Server 2012 R2 或更高版本。 应启用远程管理。
* 用于 Web 应用程序代理服务器的 Windows Server 2012 R2 或更高版本。 应启用远程管理。
* 要使用的联合身份验证服务名称（例如 sts.contoso.com）的 TLS/SSL 证书。

>[!NOTE]
>可以使用 Azure AD Connect 更新 AD FS 场的 TLS/SSL 证书，即使不使用它来管理联合身份验证信任。

### <a name="ad-fs-configuration-prerequisites"></a>AD FS 配置先决条件
若要使用 Azure AD Connect 配置 AD FS 场，请确保已在远程服务器上启用 WinRM。 确保已完成[联合身份验证先决条件](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)中的其他任务。 另请确保遵循 [Azure AD Connect 和联合身份验证/WAP 服务器](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap)表中列出的端口要求。

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>创建新的 AD FS 场或使用现有的 AD FS 场
可以使用现有的 AD FS 场，也可以创建一个新的。 如果选择创建新的场，则必须提供 TLS/SSL 证书。 如果 TLS/SSL 证书受密码保护，则系统会提示你提供密码。

![屏幕截图，显示“AD FS 场”页](./media/how-to-connect-install-custom/adfs1.png)

如果选择使用现有 AD FS 场，则会出现一个页面，你可以在其中配置 AD FS 与 Azure AD 之间的信任关系。

>[!NOTE]
>只能使用 Azure AD Connect 来管理一个 AD FS 场。 如果现有的联合身份验证信任在所选 AD FS 场上配置了 Azure AD，则 Azure AD Connect 会从头开始重新创建信任。

### <a name="specify-the-ad-fs-servers"></a>指定 AD FS 服务器
指定要在其中安装 AD FS 的服务器。 可以根据容量需求添加一个或多个服务器。 在设置此配置之前，请将所有 AD FS 服务器加入 Active Directory。 对于 Web 应用程序代理服务器，此步骤不是必需的。 

Microsoft 建议安装一台 AD FS 服务器用于测试和试验部署。 完成初始配置之后，可以通过再次运行 Azure AD Connect，根据缩放需求添加和部署更多的服务器。

> [!NOTE]
> 在设置此配置之前，请确保所有服务器已加入 Azure AD 域。
>


![屏幕截图，显示“联合服务器”页。](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>指定 Web 应用程序代理服务器
指定 Web 应用程序代理服务器。 Web 应用程序代理服务器部署在外围网络中，面向 Extranet。 它支持来自 Extranet 的身份验证请求。 可以根据容量需求添加一个或多个服务器。 

Microsoft 建议安装一台 Web 应用程序代理服务器，用于测试和试点部署。 完成初始配置之后，可以通过再次运行 Azure AD Connect，根据缩放需求添加和部署更多的服务器。 建议使用数量相当的代理服务器，以满足来自 Intranet 的身份验证要求。

> [!NOTE]
> - 如果使用的帐户不是 Web 应用程序代理服务器上的本地管理员，系统会提示你提供管理员凭据。
> - 在指定 Web 应用程序代码服务器之前，请确保 Azure AD Connect 服务器与 Web 应用程序代理服务器之间已建立 HTTP/HTTPS 连接。
> - 确保 Web 应用程序服务器与 AD FS 服务器之间的 HTTP/HTTPS 连接允许通过身份验证请求。
>


![屏幕截图，显示“Web 应用代理服务器”页。](./media/how-to-connect-install-custom/adfs3.png)

系统会提示你输入凭据，使 Web 应用程序服务器可以创建与 AD FS 服务器的安全连接。 这些凭据必须适用于 AD FS 服务器上的本地管理员帐户。

![屏幕截图，显示“凭据”页。 管理员凭据在用户名字段和密码字段中输入。](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>指定 AD FS 服务的服务帐户
AD FS 服务需要使用域服务帐户来验证用户身份以及在 Active Directory 中查找用户信息。 它可以支持两种类型的服务帐户：

* **组托管服务帐户**：此帐户类型已由 Windows Server 2012 引入 AD DS 中。 此类型的帐户提供 AD FS 之类的服务。 它是单个帐户，你不需要在其中定期更新密码。 如果 AD FS 服务器所属的域中已有 Windows Server 2012 域控制器，请使用此选项。
* **域用户帐户**：此类型的帐户会要求你提供密码，并在密码过期时定期更新密码。 仅当 AD FS 服务器所属的域中没有 Windows Server 2012 域控制器时，才使用此选项。

如果选择了“创建组托管服务帐户”但从未在 Active Directory 中使用过此功能，则输入企业管理员凭据。 这些凭据用于启动密钥存储，以及在 Active Directory 中启用该功能。

> [!NOTE]
> Azure AD Connect 会检查 AD FS 服务是否已在域中注册为服务主体名称 (SPN)。  Azure AD DS 不允许同时注册重复的 SPN。  如果发现重复的 SPN，则必须删除该 SPN 才能继续操作。

![屏幕截图，显示“AD FS 服务帐户”页。](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>选择要联合的 Azure AD 域
使用“Azure AD 域”页设置 AD FS 与 Azure AD 之间的联合关系。 在这里，请将 AD FS 配置为向 Azure AD 提供安全令牌。 另请将 Azure AD 配置为信任来自此 AD FS 实例的令牌。 

在此页上，你只能在初始安装中配置单个域。 以后可以通过再次运行 Azure AD Connect 来配置其他域。

![屏幕截图，显示“Azure AD 域”页。](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>验证选择用于联合的 Azure AD 域
当你选择要联合的域时，Azure AD Connect 会提供相关信息，供你用来验证尚未验证的域。 有关详细信息，请参阅[添加和验证域](../fundamentals/add-custom-domain.md)。

![屏幕截图，显示“Azure AD 域”页，其中包括可用来验证域的信息。](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect 会尝试在配置阶段验证域。 如果不添加所需的域名系统 (DNS) 记录，则无法完成配置。
>


## <a name="configuring-federation-with-pingfederate"></a>配置使用 PingFederate 的联合身份验证
只需单击几下鼠标，即可使用 Azure AD Connect 配置 PingFederate。 需要以下先决条件：
- PingFederate 8.4 或更高版本。  有关详细信息，请参阅 [PingFederate 与 Azure Active Directory 和 Microsoft 365 的集成](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)。
- 要使用的联合身份验证服务名称（例如 sts.contoso.com）的 TLS/SSL 证书。

### <a name="verify-the-domain"></a>验证域
在你选择使用 PingFederate 来设置联合身份验证之后，系统会要求你验证需要进行联合身份验证的域。  从下拉菜单中选择域。

![屏幕截图，显示“Azure AD 域”页。 示例域名“contoso.com”已选中。](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>导出 PingFederate 设置


将 PingFederate 配置为每个联合 Azure 域的联合服务器。  选择“导出设置”，以便与 PingFederate 管理员共享此信息。  联合服务器管理员会更新配置，然后提供 PingFederate 服务器 URL 和端口号，以便 Azure AD Connect 可以验证元数据设置。  

![屏幕截图，显示“PingFederate 设置”页。 “导出设置”按钮会显示在页面顶部附近。](./media/how-to-connect-install-custom/ping2.png)

与 PingFederate 管理员联系以解决任何验证问题。  下图显示了有关 PingFederate 服务器的信息，该服务器与 Azure 之间尚无有效的信任关系。

![屏幕截图，显示的服务器信息为：PingFederate 服务器已找到，但 Azure 的服务提供程序连接缺失或已禁用。](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>验证联合身份验证连接性
Azure AD Connect 会尝试验证它从上一步中的 PingFederate 元数据检索的身份验证终结点。  Azure AD Connect 会首先尝试使用本地 DNS 服务器来解析终结点。  接下来，它会尝试使用外部 DNS 提供程序来解析终结点。  与 PingFederate 管理员联系以解决任何验证问题。  

![屏幕截图，显示“验证连接性”页。](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>验证联合登录
最后，可以通过登录到联合域来验证新配置的联合登录流。 如果登录成功，则说明已成功配置了使用 PingFederate 的联合身份验证。

![屏幕截图，显示“验证联合登录”页。 底部的消息指示登录已成功。](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>配置和验证页面
配置发生在“配置”页上。

> [!NOTE]
> 在继续安装之前，如果配置了联合身份验证，请确保也已配置[联合服务器的名称解析](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers)。
>



![屏幕截图，显示“准备进行配置”页。](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>使用暂存模式
在暂存模式下，可以同时设置新的同步服务器。 如果要使用此设置，则只能将一台同步服务器导出到云中的一个目录。 但如果想要从另一台服务器（例如运行 DirSync 的服务器）进行迁移，则可以启用暂存模式的 Azure AD Connect。 

启用暂存设置后，同步引擎会像平时一样导入并同步数据， 但不会将任何数据导出到 Azure AD 或 Active Directory。 密码同步功能和密码写回功能在暂存模式下禁用。

![屏幕截图，显示“启用暂存模式”选项。](./media/how-to-connect-install-custom/stagingmode.png)

在暂存模式下，可以对同步引擎进行所需的更改，并查看要导出的内容。 如果配置看起来正常，请再次运行安装向导，并禁用过渡模式。 

现在，已将数据从服务器导出到 Azure AD。 确保同时禁用其他服务器，以便只有一台服务器在主动导出。

有关详细信息，请参阅[过渡模式](how-to-connect-sync-staging-server.md)。

### <a name="verify-your-federation-configuration"></a>验证联合配置
选择“验证”按钮后，Azure AD Connect 会验证 DNS 设置。 它检查以下设置：

* **Intranet 连接性**
    * 解析联合 FQDN：Azure AD Connect 会检查 DNS 是否可以解析联合 FQDN，以确保连接性。 如果 Azure AD Connect 无法解析 FQDN，则验证失败。 若要完成验证，请确保提供联合身份验证服务 FQDN 的 DNS 记录。
    * DNS A 记录：Azure AD Connect 会检查联合身份验证服务是否有 A 记录。 在没有 A 记录的情况下，验证会失败。 若要完成验证，请为联合 FQDN 创建 A 记录（不是 CNAME 记录）。
* **Extranet 连接性**
    * 解析联合 FQDN：Azure AD Connect 会检查 DNS 是否可以解析联合 FQDN，以确保连接性。

      ![屏幕截图，显示“安装完成”页。](./media/how-to-connect-install-custom/completed.png)

      ![屏幕截图，显示“安装完成”页。 消息指示 Intranet 配置已验证。](./media/how-to-connect-install-custom/adfs7.png)

若要验证端到端身份验证，请手动执行下列一个或多个测试：

* 同步完成后，在 Azure AD Connect 中使用”验证联合登录”附加任务对你选择的本地用户帐户进行身份验证。
* 在 Intranet 的已加入域的计算机上，确保你能够从浏览器登录。 连接到 https://myapps.microsoft.com。 然后使用登录帐户来验证登录。 内置的 Azure AD DS 管理员帐户未同步，你无法将它用于验证。
* 确保你能够从 Extranet 上的设备登录。 在家庭计算机或移动设备上连接到 https://myapps.microsoft.com ， 然后提供凭据。
* 验证富客户端登录。 连接到 https://testconnectivity.microsoft.com。 然后选择“Office 365” > “Office 365 单一登录测试”。 

## <a name="troubleshoot"></a>故障排除
此部分包含你在遇到 Azure AD Connect 安装问题时可以使用的故障排除信息。

若要自定义 Azure AD Connect 安装，可以在“安装所需的组件”页上选择“使用现有的 SQL Server”。 你可能会看到以下错误：“ADSync 数据库已经包含数据，无法覆盖。 请删除现有的数据库，然后重试。”

![显示“安装所需的组件”页的屏幕截图。 页面底部出现一个错误。](./media/how-to-connect-install-custom/error1.png)

你看到此错误是因为指定的 SQL Server 的 SQL 实例上已存在名为 ADSync 的数据库。

通常会在卸载 Azure AD Connect 之后看到此错误。  卸载 Azure AD Connect 时，不会从运行 SQL Server 的计算机中删除该数据库。

若要修复此问题：

1. 检查 Azure AD Connect 在卸载之前使用过的 ADSync 数据库。 确保不再使用该数据库。

2. 备份数据库。

3. 删除数据库：
    1. 使用 **Microsoft SQL Server Management Studio** 连接到 SQL 实例。 
    1. 找到 **ADSync** 数据库并右键单击它。
    1. 在上下文菜单上选择“删除”。
    1. 选择“确定”以删除数据库。

![屏幕截图，显示 Microsoft SQL Server Management Studio。 ADSync 处于选中状态。](./media/how-to-connect-install-custom/error2.png)

删除 ADSync 数据库后，请选择“安装”来重试安装。

## <a name="next-steps"></a>后续步骤
安装完成后，请注销 Windows。 然后再次登录，以便使用 Synchronization Service Manager 或同步规则编辑器。

安装 Azure AD Connect 后，就可以[验证安装并分配许可证](how-to-connect-post-installation.md)了。

若要详细了解在安装过程中启用的这些功能，请参阅[防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)和 [Azure AD Connect Health](how-to-connect-health-sync.md)。

有关其他常见主题的详细信息，请参阅 [Azure AD Connect 同步：计划程序](how-to-connect-sync-feature-scheduler.md)和[将本地标识与 Azure AD 集成](whatis-hybrid-identity.md)。
