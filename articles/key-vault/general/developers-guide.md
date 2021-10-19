---
title: Azure Key Vault 开发人员指南
description: 开发人员可以使用 Azure 密钥保管库来管理 Microsoft Azure 环境中的加密密钥。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c1b4b6437b14039284fb938405fa7069b0e239c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667547"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 开发人员指南

使用 Azure Key Vault 可以从应用程序中安全地访问敏感信息：

- 无需自己编写代码即可保护密钥、机密和证书，并且能够轻松地在应用程序中使用它们。
- 允许客户拥有和管理自己的密钥、机密和证书，以便你可以专注于提供核心软件功能。 这样，应用程序便不会对客户的租户密钥、机密和证书承担职责或潜在责任。
- 应用程序可以使用密钥进行签名和加密，不过需要使密钥管理与应用程序分开。 有关详细信息，请参阅[关于密钥](../keys/about-keys.md)。
- 可以通过将凭据（如密码、访问密钥和 SAS 令牌）作为机密存储在 Key Vault 中来对其进行管理。 有关详细信息，请参阅[关于机密](../secrets/about-secrets.md)。
- 管理证书。 有关详细信息，请参阅[关于证书](../certificates/about-certificates.md)。

有关 Azure Key Vault 的常规信息，请参阅[关于 Azure Key Vault](overview.md)。

## <a name="public-previews"></a>公共预览版

我们会定期发布新 Key Vault 功能的公共预览版。 欢迎试用公共预览功能，并通过反馈电子邮件地址 azurekeyvault@microsoft.com 将你的想法告诉我们。

## <a name="create-and-manage-key-vaults"></a>创建和管理密钥保管库

与其他 Azure 服务一样，Key Vault 通过 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)进行管理。 Azure 资源管理器是 Azure 的部署和管理服务。 可以使用它来创建、更新和删除 Azure 帐户中的资源。 

[Azure 基于角色的访问控制 (RBAC) ](../../role-based-access-control/overview.md) 可控制对管理层（也称为[管理平面](security-features.md#managing-administrative-access-to-key-vault)）的访问。 在 Key Vault 中使用管理平面来创建和管理密钥保管库及其属性（包括访问策略）。 使用数据平面来管理密钥、证书和机密。 

可以使用预定义的 Key Vault 参与者角色来授予对 Key Vault 的管理访问权限。     

### <a name="apis-and-sdks-for-key-vault-management"></a>用于密钥保管库管理的 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault)<br>[快速入门](quick-create-cli.md)|[引用](/powershell/module/az.keyvault)<br>[快速入门](quick-create-powershell.md)|[引用](/rest/api/keyvault/)|[引用](/azure/templates/microsoft.keyvault/vaults)<br>[快速入门](./vault-create-template.md)|[引用](/dotnet/api/microsoft.azure.management.keyvault)|[引用](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[引用](/java/api/com.microsoft.azure.management.keyvault)|[引用](/javascript/api/@azure/arm-keyvault)|

有关安装包和源代码，请参阅[客户端库](client-libraries.md)。

## <a name="authenticate-to-key-vault-in-code"></a>在代码中对 Key Vault 进行身份验证

Key Vault 使用 Azure Active Directory (Azure AD) 身份验证，这需要 Azure AD 安全主体授予访问权限。 Azure AD 安全主体可以是用户、应用程序服务主体、[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，也可以是任何这些类型的组。

### <a name="authentication-best-practices"></a>身份验证最佳做法

建议使用部署到 Azure 的应用程序的托管标识。 如果使用不支持托管标识的 Azure 服务或应用程序是在本地部署的，则可以选择[具有证书的服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。 在这种情况下，证书应存储在 Key Vault 中并频繁轮换。

将具有机密的服务主体用于开发和测试环境。 将用户主体用于本地开发和 Azure Cloud Shell。

建议在每个环境中使用这些安全主体：
- 生产环境：托管标识或具有证书的服务主体。
- 测试和开发环境：托管标识、具有证书的服务主体或具有机密的服务主体。
- 本地开发：具有机密的用户主体或服务主体。

### <a name="azure-identity-client-libraries"></a>Azure 标识客户端库

上述身份验证方案由 Azure 标识客户端库提供支持，并与 Key Vault SDK 集成在一起。 无需更改代码，即可跨环境和平台使用 Azure 标识客户端库。 库会自动从通过 Azure CLI、Visual Studio、Visual Studio Code 和其他方式登录到 Azure 用户的用户中检索身份验证令牌。 

有关 Azure 标识客户端库的详细信息，请参阅：

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure 标识 SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure 标识 SDK Python](/python/api/overview/azure/identity-readme)|[Azure 标识 SDK Java](/java/api/overview/azure/identity-readme)|[Azure 标识 SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

> [!Note]
> 建议 Key Vault .NET SDK 版本 3 使用[应用身份验证库](/dotnet/api/overview/azure/service-to-service-authentication)，但该版本现已弃用。 若要迁移到 Key Vault .NET SDK 版本 4，请遵循 [AppAuthentication 到 Azure.Identity 的迁移指南](/dotnet/api/overview/azure/app-auth-migration)。

有关如何在应用程序中对 Key Vault 进行身份验证的教程，请参阅：
- [将 Azure Key Vault 与通过 .NET 编写的虚拟机配合使用](./tutorial-net-virtual-machine.md)
- [将 Azure Key Vault 与通过 Python 编写的虚拟机配合使用](./tutorial-python-virtual-machine.md)
- [使用托管标识将 Key Vault 连接到 .NET 中的 Azure Web 应用](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>管理密钥、证书和机密

数据平面可控制对密钥、证书和机密的访问。 可以使用本地保管库访问策略或 Azure RBAC 进行数据平面的访问控制。

### <a name="apis-and-sdks-for-keys"></a>用于密钥的 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/key)<br>[快速入门](../keys/quick-create-cli.md)|[引用](/powershell/module/az.keyvault/)<br>[快速入门](../keys/quick-create-powershell.md)|[引用](/rest/api/keyvault/#key-operations)|[引用](/azure/templates/microsoft.keyvault/vaults/keys)<br>[快速入门](../keys/quick-create-template.md)|[引用](/dotnet/api/azure.security.keyvault.keys)<br>[快速入门](../keys/quick-create-net.md)|[引用](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[快速入门](../keys/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[快速入门](../keys/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-keys/)<br>[快速入门](../keys/quick-create-node.md)|

### <a name="apis-and-sdks-for-certificates"></a>用于证书的 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/certificate)<br>[快速入门](../certificates/quick-create-cli.md)|[引用](/powershell/module/az.keyvault)<br>[快速入门](../certificates/quick-create-powershell.md)|[引用](/rest/api/keyvault/#certificate-operations)|空值|[引用](/dotnet/api/azure.security.keyvault.certificates)<br>[快速入门](../certificates/quick-create-net.md)|[引用](/python/api/overview/azure/keyvault-certificates-readme)<br>[快速入门](../certificates/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[快速入门](../certificates/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-certificates/)<br>[快速入门](../certificates/quick-create-node.md)|

### <a name="apis-and-sdks-for-secrets"></a>用于机密的 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/secret)<br>[快速入门](../secrets/quick-create-cli.md)|[引用](/powershell/module/az.keyvault/)<br>[快速入门](../secrets/quick-create-powershell.md)|[引用](/rest/api/keyvault/#secret-operations)|[引用](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[快速入门](../secrets/quick-create-template.md)|[引用](/dotnet/api/azure.security.keyvault.secrets)<br>[快速入门](../secrets/quick-create-net.md)|[引用](/python/api/overview/azure/keyvault-secrets-readme)<br>[快速入门](../secrets/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[快速入门](../secrets/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-secrets/)<br>[快速入门](../secrets/quick-create-node.md)|

### <a name="usage-of-secrets"></a>机密用法
使用 Azure Key Vault 仅存储应用程序机密。 应存储在 Key Vault 中的机密示例包括：

- 客户端应用程序机密
- 连接字符串
- 密码
- 共享访问密钥
- SSH 密钥

任何与机密相关的信息（如用户名和应用程序 ID）都可以作为标记存储在机密中。 对于任何其他敏感配置设置，应使用 [Azure 应用配置](../../azure-app-configuration/overview.md)。
 
### <a name="references"></a>参考 

有关安装包和源代码，请参阅[客户端库](client-libraries.md)。

有关 Key Vault 数据平面安全性的信息，请参阅 [Azure Key Vault 安全功能](security-features.md)。

## <a name="use-key-vault-in-applications"></a>在应用程序中使用 Key Vault

要利用 Key Vault 中的最新功能，建议使用现有的 Key Vault SDK 以在应用程序中使用机密、证书和密钥。 Key Vault SDK 和 REST API 进行了更新，是该产品发布的新功能，它们遵循最佳做法和指导原则。

对于基本方案，还有其他简化使用的库和集成解决方案，由 Microsoft 合作伙伴或开源社区提供支持。

对于证书，可以使用：

- Key Vault 虚拟机 (VM) 扩展，它自动刷新 Azure Key Vault 中存储的证书。 有关详细信息，请参阅： 
  - [适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md)
  - [适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md)
  - [适用于已启用 Azure Arc 的服务器的 Key Vault 虚拟机扩展](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
- Azure 应用服务集成，可从 Key Vault 导入并自动刷新证书。 有关详细信息，请参阅[从 Key Vault 导入证书](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)。

对于机密，可以使用：

- Key Vault 机密，用于应用服务应用程序设置。 有关详细信息，请参阅[使用应用服务和 Azure Functions 的 Key Vault 引用](../../app-service/app-service-key-vault-references.md)。
- Key Vault 机密与应用程序配置服务，用于托管在 Azure VM 中的应用程序。 有关详细信息，请参阅[使用应用程序配置和 Key Vault 配置应用程序](/samples/azure/azure-sdk-for-net/app-secrets-configuration/)。

## <a name="code-examples"></a>代码示例

有关将 Key Vault 用于应用程序的完整示例，请参阅 [Azure Key Vault 代码示例](https://azure.microsoft.com/resources/samples/?service=key-vault)。 

## <a name="task-specific-guidance"></a>任务特定指南

以下文章和方案提供了特定于任务的指导，方便用户使用 Azure Key Vault：

- 要访问密钥保管库，客户端应用程序应能够访问各种功能的多个终结点。 请参阅[访问防火墙保护下的 Key Vault](access-behind-firewall.md)。 
- 在 Azure VM 中运行的云应用程序需要证书。 如何将此证书部署到此 VM 中？ 请参阅[适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md)和[适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md)。
- 若要使用 Azure CLI、PowerShell 或 Azure 门户分配访问策略，请参阅[分配 Key Vault 访问策略](assign-access-policy.md)。 
- 有关启用了软删除的密钥保管库和各种密钥保管库对象的使用和生命周期的指导，请参阅[通过软删除和清除保护实现 Azure Key Vault 恢复管理](./key-vault-recovery.md)。
- 需要在部署期间以参数形式传递安全值（例如密码）时，可以将该值存储为密钥保管库中的机密，然后在其他资源管理器模板中引用该值。 请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../../azure-resource-manager/templates/key-vault-parameter.md)。

## <a name="integration-with-key-vault"></a>与 Key Vault 集成

以下服务和方案使用 Key Vault 或与 Key Vault 集成：

- [静态加密](../../security/fundamentals/encryption-atrest.md)允许在持久保存数据时对数据进行编码（加密）。 数据加密密钥通常由 Azure Key Vault 中的密钥加密密钥进行加密，以进一步限制访问。
- [Azure 信息保护](/azure/information-protection/plan-implement-tenant-key)允许管理自己的租户密钥。 例如，不是由 Microsoft 管理租户密钥（默认设置），可以管理自己的租户密钥，以遵守适用于组织的具体规定。 管理自己的租户密钥也称为自带密钥 (BYOK)。
- 使用 [Azure 专用链接](private-link-service.md)，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如 Azure Key Vault、Azure 存储和 Azure Cosmos DB）以及 Azure 托管的客户服务/合作伙伴服务。
- 通过将 Key Vault 与 [Azure 事件网格](../../event-grid/event-schema-key-vault.md)集成，用户可以在密钥保管库中存储的机密的状态发生更改时收到通知。 可以将新版本的机密分发到应用程序，也可以轮换即将到期的机密，以防止中断。
- 防止自己的 [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) 机密在 Key Vault 中被意外访问。
- 使用 Key Vault 中存储的机密[从 Azure Databricks 连接到 Azure 存储](./integrate-databricks-blob-storage.md)。
- 为 Kubernetes 上的[机密存储 CSI 驱动程序](./key-vault-integrate-kubernetes.md)配置并运行 Azure Key Vault 提供程序。 

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 概述和概念

若要了解：

- 可以恢复已删除的对象的功能（不管是有意还是无意删除），请参阅 [Azure Key Vault 软删除概述](soft-delete-overview.md)。
- 限制的基本概念并获得针对应用的限制方法，请参阅 [Azure Key Vault 限制指南](overview-throttling.md)。
- 区域和安全区域之间的关系，请参阅 [Azure Key Vault 安全体系和地理边界](overview-security-worlds.md)。

## <a name="social"></a>社交

- [Key Vault 博客](/archive/blogs/kv/)
- [Microsoft 问答](https://docs.microsoft.com/answers/products/)
- [Key Vault 的 Stack Overflow](https://stackoverflow.com/questions/tagged/azure-keyvault)
