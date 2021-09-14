---
title: Azure 密钥保管库开发人员指南
description: 开发人员可以使用 Azure 密钥保管库来管理 Microsoft Azure 环境中的加密密钥。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 548dcc59ae0303022f4807add5764b55b313f683
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438941"
---
# <a name="azure-key-vault-developers-guide"></a>Azure 密钥保管库开发人员指南

使用 Key Vault 可以从应用程序中安全地访问敏感信息：

- 无需自己编写代码即可保护密钥、机密和证书，并且能够轻松地在应用程序中使用它们。
- 允许客户拥有和管理自己的密钥、机密和证书，以便你可以专注于提供核心软件功能。 这样，应用程序便不会对客户的租户密钥、机密和证书承担职责或潜在责任。
- 应用程序可以使用密钥进行签名和加密，不过需要使密钥管理与应用程序分开。 有关密钥的详细信息，请参阅[关于密钥](../keys/about-keys.md)
- 可以通过将凭据（如密码、访问密钥和 SAS 令牌）作为机密存储在 Key Vault 中来对其进行管理，请参阅[关于秘密](../secrets/about-secrets.md)
- 管理证书。 有关详细信息，请参阅[关于证书](../certificates/about-certificates.md)

有关 Azure Key Vault 的更多常规信息，请参阅[什么是 Key Vault](overview.md)。

## <a name="public-previews"></a>公共预览版

我们会定期发布新 Key Vault 功能的公共预览版。 欢迎试用公共预览功能，并通过反馈电子邮件地址 azurekeyvault@microsoft.com 将你的想法告诉我们。

## <a name="creating-and-managing-key-vaults"></a>创建和管理密钥保管库

与其他 Azure 服务类似，Key Vault 管理也是通过 Azure 资源管理器服务完成的。 Azure 资源管理器是 Azure 的部署和管理服务。 它提供了一个管理层，用于在 Azure 帐户中创建、更新和删除资源。 有关详细信息，请参阅 [Azure 资源管理器 API](../../azure-resource-manager/management/overview.md)

对管理层的访问由 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)控制。 在 Key Vault 的管理层（也称为管理或控制平面）中，可以创建和管理 Key Vault 及其属性，包括访问策略，但不包括在数据平面上托管的密钥、机密和证书。 可以使用预定义的 `Key Vault Contributor` 角色来授予对 Key Vault 的管理访问权限。     

**用于密钥保管库管理的 API 和 SDK：**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault)<br>[快速入门](quick-create-cli.md)|[引用](/powershell/module/az.keyvault)<br>[快速入门](quick-create-powershell.md)|[引用](/rest/api/keyvault/)|[引用](/azure/templates/microsoft.keyvault/vaults)<br>[快速入门](./vault-create-template.md)|[引用](/dotnet/api/microsoft.azure.management.keyvault)|[引用](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[引用](/java/api/com.microsoft.azure.management.keyvault)|[引用](/javascript/api/@azure/arm-keyvault)|

有关安装包和源代码的信息，请参阅[客户端库](client-libraries.md)。

有关 Key Vault 管理平面的详细信息，请参阅 [Azure Key Vault 安全功能](security-features.md)

## <a name="authenticate-to-key-vault-in-code"></a>在代码中对 Key Vault 进行身份验证

Key Vault 使用的 Azure AD 身份验证要求 Azure AD 安全主体授予访问权限。 Azure AD 安全主体可以是用户、应用程序服务主体、[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，也可以是任何类型的安全主体的组。

### <a name="authentication-best-practices"></a>身份验证最佳做法

建议对部署到 Azure 的应用程序使用托管标识。 如果使用不支持托管标识的 Azure 服务或应用程序是在本地部署的，则可以选择[有证书的服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。 在这种情况下，证书应存储在 Key Vault 中并频繁轮换。 具有机密的服务主体可用于开发和测试环境，建议在本地或在 Cloud Shell 中使用用户主体。

每个环境的建议安全主体：
- **生产环境**：
  - 托管标识或具有证书的服务主体
- **测试和开发环境**：
  - 托管标识、具有证书的服务主体或具有机密的服务主体
- **本地开发**：
  - 具有机密的用户主体或服务主体

上述身份验证方案由 Azure 标识客户端库提供支持，并与 Key Vault SDK 集成在一起。 无需更改代码即可在不同的环境和平台上使用 Azure 标识库。 Azure 标识还将使用 Azure CLI、Visual Studio、Visual Studio Code 等从登录到 Azure 的用户中自动检索身份验证令牌。 

有关 Azure 标识客户端库的详细信息，请参阅：

**Azure 标识客户端库**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure 标识 SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure 标识 SDK Python](/python/api/overview/azure/identity-readme)|[Azure 标识 SDK Java](/java/api/overview/azure/identity-readme)|[Azure 标识 SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [应用身份验证库](/dotnet/api/overview/azure/service-to-service-authentication)（目前已弃用，建议使用 Key Vault .NET SDK 版本 3）。 请按照 [AppAuthentication to Azure.Identity 迁移指南](/dotnet/api/overview/azure/app-auth-migration)迁移到 Key Vault .NET SDK 版本 4。

有关如何在应用程序中对 Key Vault 进行身份验证的教程，请参阅：
- [在 .NET 的 VM 中托管的应用程序中对 Key Vault 进行身份验证](./tutorial-net-virtual-machine.md)
- [在 Python 的 VM 中托管的应用程序中对 Key Vault 进行身份验证](./tutorial-python-virtual-machine.md)
- [通过应用服务对 Key Vault 进行身份验证](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>管理密钥、证书和机密

对密钥、机密和证书的访问由数据平面控制。 可以使用本地保管库访问策略或 Azure RBAC 完成数据平面访问控制。

### <a name="keys-apis-and-sdks"></a>密钥 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/key)<br>[快速入门](../keys/quick-create-cli.md)|[引用](/powershell/module/az.keyvault/)<br>[快速入门](../keys/quick-create-powershell.md)|[引用](/rest/api/keyvault/#key-operations)|[引用](/azure/templates/microsoft.keyvault/vaults/keys)<br>[快速入门](../keys/quick-create-template.md)|[引用](/dotnet/api/azure.security.keyvault.keys)<br>[快速入门](../keys/quick-create-net.md)|[引用](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[快速入门](../keys/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[快速入门](../keys/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-keys/)<br>[快速入门](../keys/quick-create-node.md)|

### <a name="certificates-apis-and-sdks"></a>证书 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/certificate)<br>[快速入门](../certificates/quick-create-cli.md)|[引用](/powershell/module/az.keyvault)<br>[快速入门](../certificates/quick-create-powershell.md)|[引用](/rest/api/keyvault/#certificate-operations)|空值|[引用](/dotnet/api/azure.security.keyvault.certificates)<br>[快速入门](../certificates/quick-create-net.md)|[引用](/python/api/overview/azure/keyvault-certificates-readme)<br>[快速入门](../certificates/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[快速入门](../certificates/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-certificates/)<br>[快速入门](../certificates/quick-create-node.md)|

### <a name="secrets-apis-and-sdks"></a>机密 API 和 SDK

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/secret)<br>[快速入门](../secrets/quick-create-cli.md)|[引用](/powershell/module/az.keyvault/)<br>[快速入门](../secrets/quick-create-powershell.md)|[引用](/rest/api/keyvault/#secret-operations)|[引用](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[快速入门](../secrets/quick-create-template.md)|[引用](/dotnet/api/azure.security.keyvault.secrets)<br>[快速入门](../secrets/quick-create-net.md)|[引用](/python/api/overview/azure/keyvault-secrets-readme)<br>[快速入门](../secrets/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[快速入门](../secrets/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-secrets/)<br>[快速入门](../secrets/quick-create-node.md)|

### <a name="secrets-usage"></a>机密使用情况
Azure Key Vault 机密组件只能用于为应用程序存储机密。 对于任何非机密对象，应使用 [Azure 应用程序配置](https://docs.microsoft.com/azure/azure-app-configuration/overview)。

- 应存储在 Key Vault 中的机密的示例：
    - 客户端应用程序机密
    - 连接字符串
    - 密码
    - 共享访问密钥
    - SSH 密钥

- 应存储在应用程序配置中的非机密的示例：
    - 客户端应用程序 ID
    - IP 地址
    - 服务终结点
    - 服务配置参数
    - 用户名

### <a name="references"></a>参考 

有关安装包和源代码的信息，请参阅[客户端库](client-libraries.md)。

有关 Key Vault 数据平面安全性的详细信息，请参阅 [Azure Key Vault 安全功能](security-features.md)。

### <a name="using-key-vault-in-applications"></a>在应用程序中使用 Key Vault

建议使用现有的 Key Vault SDK 以在应用程序中使用机密、证书和密钥，从而利用 Key Vault 中的最新功能。 Key Vault SDK 和 REST API 由我们的团队发布，因为我们为该产品发布了新功能，并遵循我们的最佳做法和指导原则。

#### <a name="libraries-and-integration-solutions-for-limited-usage-scenarios"></a>适用于有限使用方案的库和集成解决方案
对于基本方案，还有其他简化使用的解决方案，由合作伙伴团队或开源社区提供支持。

##### <a name="certificates"></a>证书：
- 通过 Key Vault VM 扩展，可自动刷新 Azure Key Vault 库中存储的证书。 
    - [适用于 Windows 的 Key Vault 虚拟机扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)
    - [适用于 Linux 的 Key Vault 虚拟机扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux)
    - [适用于已启用 Azure Arc 的服务器的 Key Vault 虚拟机扩展](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#azure-key-vault-vm-extension)
 - Azure 应用服务 Key Vault 证书集成，可从 Key Vault 导入并自动刷新证书
     - [通过 Key Vault 部署 Azure Web 应用证书](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)

##### <a name="secrets"></a>机密：
- 将 Key Vault 机密用于应用服务应用程序设置
    - [使用应用服务和 Azure Functions 的 Key Vault 引用](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)
- 将 Key Vault 机密与应用程序配置服务用于托管在 Azure VM 中的应用程序
    - [使用应用程序配置和 Key Vault 配置应用程序](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/app-secrets-configuration/)

## <a name="code-examples"></a>代码示例

有关在应用程序中使用密钥保管库的完整示例，请参阅：

- [Azure Key Vault 代码示例](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault 的代码示例。 

## <a name="how-tos"></a>操作方法

以下文章和方案提供了特定于任务的指导，方便用户使用 Azure Key Vault：

- [访问防火墙后面的密钥保管库](access-behind-firewall.md) - 若要访问密钥保管库，密钥保管库客户端应用程序需要能够访问多个终结点才能使用各种功能。
- 如何将证书从 Key Vault 部署到 VM - [Windows](../../virtual-machines/extensions/key-vault-windows.md)、[Linux](../../virtual-machines/extensions/key-vault-linux.md) - 在 Azure VM 中运行的云应用程序需要证书。 现在，如何将此证书部署到此 VM 中？
- 分配访问策略（[CLI](assign-access-policy-cli.md) | [PowerShell](assign-access-policy-powershell.md) | [门户](assign-access-policy-portal.md)）。 
- [如何将 Key Vault 软删除与 CLI 配合使用](./key-vault-recovery.md)介绍了 Key Vault 的使用和生命周期以及各种已启用软删除的 Key Vault 对象。
- [如何在部署期间传递安全值（如密码）](../../azure-resource-manager/templates/key-vault-parameter.md) - 需要在部署期间以参数形式传递安全值（例如密码）时，可以将该值存储为 Azure Key Vault 中的机密，并在其他资源管理模板中引用该值。

## <a name="integrated-with-key-vault"></a>与密钥保管库集成

这些文章介绍了使用 Key Vault 或与之集成的其他方案和服务。

- [静态加密](../../security/fundamentals/encryption-atrest.md)可以在持久保存数据时对数据进行编码（加密）。 数据加密密钥通常由 Azure Key Vault 中的密钥加密密钥进行加密，以进一步限制访问。
- [Azure 信息保护](/azure/information-protection/plan-implement-tenant-key)允许管理自己的租户密钥。 例如，不是由 Microsoft 管理租户密钥（默认设置），可以管理自己的租户密钥，以遵守适用于组织的具体规定。 管理自己的租户密钥也称为自带密钥（简称 BYOK）。
- 使用 [Azure 专用链接服务](private-link-service.md)，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如 Azure Key Vault、Azure 存储和 Azure Cosmos DB）以及 Azure 托管的客户服务/合作伙伴服务。
- 通过将 Key Vault 与[事件网格](../../event-grid/event-schema-key-vault.md)集成，用户可以在密钥保管库中存储的机密的状态发生更改时收到通知。 可以将新版本的机密分发到应用程序，也可以轮换即将到期的机密，以防止中断。
- 可以防止自己的 [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) 机密在 Key Vault 中被意外访问。
- [使用 DataBricks 的密钥保管库中存储的机密连接到 Azure 存储](./integrate-databricks-blob-storage.md)
- 为 Kubernetes 上的[机密存储 CSI 驱动程序](./key-vault-integrate-kubernetes.md)配置并运行 Azure Key Vault 提供程序

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 概述和概念

- [Key Vault 软删除行为](soft-delete-overview.md)介绍了一种可以恢复已删除的对象的功能（不管是有意还是无意删除）。
- [Key Vault 客户端限制](overview-throttling.md)介绍了有关限制的基本概念，并针对应用提供了限制方法。
- [Key Vault 安全体系](overview-security-worlds.md)介绍了区域与安全领域之间的关系。

## <a name="social"></a>社交

- [密钥保管库博客](/archive/blogs/kv/)
- [密钥保管库论坛](https://aka.ms/kvforum)
- [Key Vault 的 Stack Overflow](https://stackoverflow.com/questions/tagged/azure-keyvault)
