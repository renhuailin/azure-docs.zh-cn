---
title: Azure 自动化帐户身份验证概述
description: 本文概述了 Azure 自动化帐户身份验证。
keywords: 自动化安全性, 安全的自动化; 自动化身份验证
services: automation
ms.subservice: process-automation
ms.date: 08/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5a86a5c8c0922e0861411e93376047344ba6c5af
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789097"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure 自动化帐户身份验证概述

Azure 自动化让可以通过其他云提供程序（如 Amazon Web Services (AWS)）针对 Azure、本地中的资源来自动执行任务。 可以使用 Runbook 来自动完成任务；如果你有要在 Azure 外部管理的业务或操作过程，也可以使用混合 Runbook 辅助角色。 在上述任一环境中工作都需要权限，以使用所需的最小权限安全地访问资源。

本文介绍了 Azure 自动化支持的身份验证方案，以及如何基于你需要管理的一个或多个环境开始操作。

## <a name="automation-account"></a>自动化帐户

首次启动 Azure 自动化时，必须创建至少一个自动化帐户。 使用 Azure 自动化帐户，你可以将 Azure 自动化资源、Runbook、资产、配置与其他帐户的资源相隔离。 可以使用 Azure 自动化帐户将资源分成单独的逻辑环境或委派的职责。 例如，可以在开发环境中使用一个帐户，在生产环境中使用另一个帐户，并在本地环境中使用另一个账户。 也可以使用[更新管理](update-management/overview.md)来指定一个 Azure 自动化帐户管理所有计算机上的操作系统更新。 

Azure 自动化帐户不同于 Microsoft 帐户或在 Azure 订阅中创建的帐户。 有关创建自动化帐户的介绍，请参阅[创建自动化帐户](./quickstarts/create-account-portal.md)。

## <a name="automation-resources"></a>自动化资源

每个自动化帐户的自动化资源都与单个 Azure 区域相关联，但该帐户可以管理 Azure 订阅中的所有资源。 如果你的策略要求将数据和资源隔离到特定的区域，则这是在不同区域中创建自动化帐户的主要原因。

在 Azure 自动化中使用 Azure 资源管理器和 PowerShell cmdlet 针对资源创建的所有任务必须使用基于 Azure Active Directory (Azure AD) 组织标识凭据的身份验证向 Azure 进行身份验证。

## <a name="managed-identities-preview"></a>托管标识（预览）

借助 Azure Active Directory (Azure AD) 的托管标识，runbook 可以轻松访问其他受 Azure AD 保护的资源。 标识由 Azure 平台托管，无需预配或轮换任何机密。 有关 Azure AD 中的托管标识的详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

下面是使用托管标识的一些好处：

- 使用托管标识而非自动化运行方式帐户可以简化管理。 你不需要续订运行方式帐户所使用的证书。

- 无需额外付费也可使用托管标识。

- 你不需要续订自动化运行方式帐户所使用的证书。

- 不必在 runbook 代码中指定运行方式连接对象。 可以使用自动化帐户的托管标识通过 runbook 访问资源，而无需创建证书、连接、运行方式帐户等。

可以向自动化帐户授予两种类型的标识：

- 系统分配的标识与你的应用程序相绑定，如果删除应用，标识也会被删除。 一个应用只能具有一个系统分配的标识。

- 用户分配的标识是可以分配给应用的独立 Azure 资源。 一个应用可以具有多个用户分配的标识。

> [!NOTE]
> 仅云作业支持用户分配的标识。 若要详细了解不同的托管标识，请参阅[管理标识类型](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。

有关使用托管标识的详细信息，请参阅[为 Azure 自动化（预览）启用托管标识](enable-managed-identity-for-automation.md)。

## <a name="run-as-accounts"></a>运行方式帐户

Azure 自动化中的运行方式帐户提供的身份验证适用于管理 Azure 资源管理器资源或在经典部署模型上部署的资源。 Azure 自动化中有两种类型的运行方式帐户：

若要创建或续订运行方式帐户，需要 3 个级别的权限：

- 订阅、
- Azure Active Directory (Azure AD) 和
- 自动化帐户

### <a name="subscription-permissions"></a>订阅权限

你需要 `Microsoft.Authorization/*/Write` 权限。 此权限是通过以下 Azure 内置角色之一的成员资格获得的：

- [所有者](../role-based-access-control/built-in-roles.md#owner)
- [用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)

若要配置或续订经典运行方式帐户，需要在订阅级别具有共同管理员角色。 若要详细了解有关经典订阅权限，请参阅 [Azure 经典订阅管理员](../role-based-access-control/classic-administrators.md#add-a-co-administrator)。

### <a name="azure-ad-permissions"></a>Azure AD 权限

为了能够创建或续订服务主体，需要成为以下 Azure AD 内置角色之一的成员：

- [应用程序管理员](../active-directory/roles/permissions-reference.md#application-administrator)
- [应用程序开发人员](../active-directory/roles/permissions-reference.md#application-developer)

可在目录级别将成员资格分配给租户中的所有用户，这是默认行为。 你可在目录级别向任一角色授予成员资格。 有关详细信息，请参阅[谁有权向我的 Azure AD 实例添加应用程序？](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

### <a name="automation-account-permissions"></a>自动化帐户权限

为了能够创建或更新自动化帐户，你需要成为以下自动化帐户角色之一的成员：

- [所有者](./automation-role-based-access-control.md#owner)
- [参与者](./automation-role-based-access-control.md#contributor)
- [自定义 Azure 自动化参与者](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)

若要了解有关 Azure 资源管理器和经典部署模型的详细信息，请参阅[资源管理器与经典部署](../azure-resource-manager/management/deployment-models.md)。

>[!NOTE]
>Azure 云解决方案提供商 (CSP) 订阅仅支持 Azure 资源管理器模型。 非 Azure 资源管理器服务在计划中不可用。 使用 CSP 订阅时，不会创建 Azure 经典运行方式帐户，但会创建 Azure 运行方式帐户。 若要了解有关 CSP 订阅的详细信息，请参阅 [CSP 订阅中可用的服务](/azure/cloud-solution-provider/overview/azure-csp-available-services)。

默认情况下，创建 Azure 自动化帐户时，会同时使用自签名证书创建运行方式帐户。 如果选择不与 Azure 自动化帐户一起创建运行方式帐户，则可以稍后单独创建它。 Azure 经典运行方式帐户是可选的，如果需要管理经典资源，则可以单独创建。

如果要使用企业或第三方证书颁发机构 (CA) 颁发的证书代替默认的自签名证书，可对运行方式帐户和经典运行方式帐户使用[用于创建运行方式帐户的 PowerShell 脚本](create-run-as-account.md#powershell-script-to-create-a-run-as-account)选项。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>运行方式帐户

创建运行方式帐户时，它会执行以下任务：

* 创建使用自签名证书的 Azure AD 应用程序，在 Azure AD 中为此应用程序创建服务主体帐户，并在当前订阅中为此帐户分配[参与者](../role-based-access-control/built-in-roles.md#contributor)角色。 可将证书设置更改为[读取者](../role-based-access-control/built-in-roles.md#reader)或其他任何角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。

* 在指定的自动化帐户中创建名为 `AzureRunAsCertificate` 的自动化证书资产。 该证书资产保存 Azure AD 应用程序使用的证书私钥。

* 在指定的自动化帐户中创建名为 `AzureRunAsConnection` 的自动化连接资产。 该连接资产保存应用程序 ID、租户 ID、订阅 ID 和证书指纹。

### <a name="azure-classic-run-as-account"></a>Azure 经典运行方式帐户

创建 Azure 经典运行方式帐户时会执行以下任务：

> [!NOTE]
> 只有订阅的共同管理员才能创建或续订这种类型的运行方式帐户。

* 在订阅中创建管理证书。

* 在指定的自动化帐户中创建名为 `AzureClassicRunAsCertificate` 的自动化证书资产。 该证书资产保存管理证书使用的证书私钥。

* 在指定的自动化帐户中创建名为 `AzureClassicRunAsConnection` 的自动化连接资产。 该连接资产保存订阅名称、订阅 ID 和证书资产名称。

## <a name="service-principal-for-run-as-account"></a>运行方式帐户的服务主体

默认情况下，运行方式帐户的服务主体没有读取 Azure AD 的权限。 如果你希望添加读取或管理 Azure AD 的权限，需要在“API 权限”下对服务主体授予该权限。 若要了解详细信息，请参阅[添加用于访问 Web API 的权限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>运行方式帐户的权限

本部分定义普通运行方式帐户和经典运行方式帐户的权限。

* 若要创建或更新运行方式帐户，Azure Active Directory 中的应用程序管理员和订阅中的所有者可以完成所有任务。
* 若要配置或续订经典运行方式帐户，需要在订阅级别具有共同管理员角色。 若要详细了解有关经典订阅权限，请参阅 [Azure 经典订阅管理员](../role-based-access-control/classic-administrators.md#add-a-co-administrator)。

下表显示了在实施职责分离的情况下，所需的任务、等效 cmdlet 和权限的列表：

|任务|Cmdlet  |最低权限  |设置权限的位置|
|---|---------|---------|---|
|创建 Azure AD 应用程序|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | 应用程序开发人员角色<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>主页 > Azure AD > 应用注册 |
|将凭据添加到应用程序。|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | 应用程序管理员或全局管理员<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>主页 > Azure AD > 应用注册|
|创建和获取 Azure AD 服务主体|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | 应用程序管理员或全局管理员<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>主页 > Azure AD > 应用注册|
|分配或获取指定主体的 Azure 角色|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | 用户访问管理员或所有者，或具有以下权限：</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [订阅](../role-based-access-control/role-assignments-portal.md)</br>主页 > 订阅 > \<subscription name\> - 访问控制 (IAM)|
|创建或删除自动化证书|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | 资源组中的参与者         |自动化帐户资源组|
|创建或删除自动化连接|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|资源组中的参与者 |自动化帐户资源组|

<sup>1</sup> Azure AD 租户中的非管理员用户可以[注册 AD 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)，前提是 Azure AD 租户的“用户设置”页中的“用户可以注册应用程序”选项已设置为“是”  。 如果应用程序注册设置为“否”，则执行此操作的用户必须具有此表中定义的角色。

如果你在被添加到订阅的全局管理员角色之前不是订阅的 Active Directory 实例的成员，则会将你添加为来宾。 在这种情况下，将在“添加自动化帐户”页上收到 `You do not have permissions to create…` 警告。

若要验证生成错误消息的情况是否已解决：

1. 在 Azure 门户的“Azure Active Directory”窗格中，选择“用户和组”。
2. 选择“所有用户”。
3. 选择名称，然后选择“配置文件”。
4. 确保用户配置文件下的“用户类型”属性的值未设置为“来宾”。 

## <a name="role-based-access-control"></a>基于角色的访问控制

基于角色的访问控制在 Azure 资源管理器中可用，用于向 Azure AD 用户帐户和运行方式帐户授予允许的操作，并对服务主体进行身份验证。 请阅读 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文，详细了解如何开发自动化权限管理模型。

如果对资源组中的权限分配具有严格的安全控制，则需要将运行方式帐户成员身份分配给资源组中的“参与者”角色。

> [!NOTE]
> 建议你不要使用 **Log Analytics 参与者** 角色来执行自动化作业。 而是创建 Azure 自动化参与者自定义角色，并将其用于与自动化帐户相关的操作。 有关详细信息，请参阅[自定义 Azure 自动化参与者角色](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)。

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>使用混合 Runbook 辅助角色的 Runbook 身份验证

在数据中心的混合 Runbook 辅助角色上运行的或针对其他云环境（如 AWS）中的计算服务运行的 Runbook 不能使用通常用于向 Azure 资源进行Runbook 身份验证的方法。 这是因为这些资源在 Azure 外部运行，因此，它们需要在自动化中定义自己的安全凭据，以便向需要在本地访问的资源进行身份验证。 有关使用 Runbook 辅助角色的 Runbook 身份验证的详细信息，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

对于在 Azure VM 上使用混合 Runbook 辅助角色的 Runbook，可以通过[使用托管标识的 Runbook 身份验证](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)而不是运行方式帐户来向 Azure 资源进行身份验证。

## <a name="next-steps"></a>后续步骤

* 若要从 Azure 门户创建自动化帐户，请参阅[创建独立的 Azure 自动化帐户](automation-create-standalone-account.md)。
* 如果你喜欢使用模板创建帐户，请参阅[使用 Azure 资源管理器模板创建自动化帐户](quickstart-create-automation-account-template.md)。
* 有关如何使用 Amazon Web Services 进行身份验证，请参阅[使用 Amazon Web Services 对 Runbook 进行身份验证](automation-config-aws-account.md)。
* 如需支持 Azure 资源托管标识功能的 Azure 服务的列表，请参阅[支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。