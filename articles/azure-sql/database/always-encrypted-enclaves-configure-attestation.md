---
title: 为 Azure SQL 逻辑服务器配置 Azure 证明
description: 在 Azure SQL 数据库中为具有安全 enclave 的 Always Encrypted 配置 Azure 证明。
keywords: 加密数据, sql 加密, 数据库加密, 敏感数据, 始终加密, 安全 enclave, SGX, 证明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: fb42a0428f0439053375027481d38977b068e356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122572"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>为 Azure SQL 逻辑服务器配置 Azure 证明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库的具有安全 Enclave 的 Always Encrypted 目前提供公共预览版。

[Microsoft Azure 证明](../../attestation/overview.md)是证明受信任执行环境 (TEE) 的解决方案，包括 Intel Software Guard Extensions (Intel SGX) enclave。 

若要在 Azure SQL 数据库中使用 Azure 证明来证明用于[具有安全 enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 的 Intel SGX enclave，需执行以下操作：

1. 创建[证明提供程序](../../attestation/basic-concepts.md#attestation-provider)，并使用建议证明策略进行配置。

2. 授予 Azure SQL 逻辑服务器对证明提供程序的访问权限。

> [!NOTE]
> 配置证明是证明管理员的职责。 请参阅[配置 SGX enclave 和证明时的角色和职责](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

## <a name="requirements"></a>要求

Azure SQL 逻辑服务器和证明提供程序必须属于同一个 Azure Active Directory 租户。 不支持跨租户交互。 

Azure SQL 逻辑服务器必须分配有 Azure AD 标识。 作为证明管理员，你需要从该服务器的 Azure SQL 数据库管理员处获取服务器的 Azure AD 标识。 你会使用该标识向服务器授予对证明提供程序的访问权限。 

有关如何使用 PowerShell 和 Azure CLI 创建具有标识的服务器或向现有服务器分配标识的说明，请参阅[将 Azure AD 标识分配到服务器](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)。

## <a name="create-and-configure-an-attestation-provider"></a>创建和配置证明提供程序

[证明提供程序](../../attestation/basic-concepts.md#attestation-provider)是 Azure 证明中的一种资源，可按照[证明策略](../../attestation/basic-concepts.md#attestation-request)评估[证明请求](../../attestation/basic-concepts.md#attestation-request)并颁发[证明令牌](../../attestation/basic-concepts.md#attestation-token)。 

证明策略使用[声明规则语法](../../attestation/claim-rule-grammar.md)进行指定。

对于在 Azure SQL 数据库中证明用于 Always Encrypted 的 Intel SGX enclave，Microsoft 建议使用以下策略：

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

以上策略验证：

- Azure SQL 数据库中的 enclave 不支持调试。 
  > 可在禁用或启用调试的情况下加载 enclave。 调试支持旨在使开发人员可以对 enclave 中运行的代码进行故障排除。 在生产系统中，调试可以让管理员检查 enclave 的内容，这会降低 enclave 提供的保护级别。 建议策略会禁用调试，以确保在恶意管理员尝试通过接管 enclave 计算机来启用调试支持时，证明会失败。 
- enclave 的产品 ID 与分配给具有安全 enclave 的 Always Encrypted 的产品 ID 匹配。
  > 每个 enclave 都具有唯一的产品 ID，可将该 enclave 与其他 enclave 区分开来。 分配给 Always Encrypted enclave 的产品 ID 是 4639。 
- 库的安全版本号 (SVN) 大于 0。
  > SVN 使 Microsoft 可以响应 enclave 代码中标识的潜在安全 bug。 如果发现并修复了安全问题，则 Microsoft 会部署具有新（递增）SVN 的新版本 enclave。 以上建议策略会进行更新以反映新 SVN。 通过更新策略以匹配建议策略，你可以确保在恶意管理员尝试加载较旧且不安全的 enclave 时，证明会失败。
- enclave 中的库使用 Microsoft 签名密钥进行了签名（x-ms-sgx-mrsigner 声明的值是签名密钥的哈希）。
  > 证明的主要目标之一是说服客户端，使之相信 enclave 中运行的二进制文件便是应该运行的二进制文件。 证明策略提供了两种机制来实现此目标。 一种是 mrenclave 声明，它是应在 enclave 中运行的二进制文件的哈希。 mrenclave 的问题在于，即使对代码进行了微小更改，二进制文件哈希也会更改，这使得难以修订在 enclave 中运行的代码。 因此，建议使用 mrsigner，它是用于对 enclave 二进制文件进行签名的密钥的哈希。 当 Microsoft 修订 enclave 时，只要签名密钥不变，mrsigner 便会保持不变。 这样，便可以在不中断客户应用程序的情况下部署更新的二进制文件。 

> [!IMPORTANT]
> 证明提供程序使用 Intel SGX enclave 的默认策略进行创建，该策略不会验证在 enclave 内运行的代码。 Microsoft 强烈建议为具有安全 enclave 的 Always Encrypted 设置以上建议策略，而不是使用默认策略。

有关如何创建证明提供程序并使用证明策略进行配置的说明，请参阅：

- [快速入门：使用 Azure 门户设置 Azure 证明](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > 使用 Azure 门户配置证明策略时，请将“证明类型”设置为 `SGX-IntelSDK`。
- [快速入门：使用 Azure PowerShell 设置 Azure 证明](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > 使用 Azure PowerShell 配置证明策略时，请将 `Tee` 参数设置为 `SgxEnclave`。
- [快速入门：使用 Azure CLI 设置 Azure 证明](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > 使用 Azure CLI 配置证明策略时，请将 `attestation-type` 参数设置为 `SGX-IntelSDK`。

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>确定证明策略的证明 URL

配置证明策略后，需要向在 Azure SQL 数据库中使用具有安全 enclave 的 Always Encrypted 的应用程序管理员共享证明 URL，从而引用策略。 应用程序管理员或/和应用程序用户需要使用证明 URL 配置其应用，以便他们可以运行使用安全 enclave 的语句。

### <a name="use-powershell-to-determine-the-attestation-url"></a>使用 PowerShell 确定证明 URL

使用以下脚本可确定证明 URL：

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>使用 Azure 门户确定证明 URL

1. 在证明提供程序的“概述”窗格中，将“证明 URI”属性的值复制到剪贴板。 证明 URI 应如下所示：`https://MyAttestationProvider.us.attest.azure.net`。

2. 将以下内容追加到证明 URI：`/attest/SgxEnclave`。 

得到的证明 URL 应如下所示：`https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>授予 Azure SQL 逻辑服务器对证明提供程序的访问权限

在证明工作流过程中，包含数据库的 Azure SQL 逻辑服务器会调用证明提供程序以提交证明请求。 若要使 Azure SQL 逻辑服务器能够提交证明请求，服务器必须对证明提供程序拥有 `Microsoft.Attestation/attestationProviders/attestation/read` 操作权限。 授予该权限的建议方法是让证明提供程序的管理员将服务器的 Azure AD 标识分配给证明提供程序或其包含资源组的证明读取者角色。

### <a name="use-azure-portal-to-assign-permission"></a>使用 Azure 门户分配权限

若要将 Azure SQL 服务器的标识分配给证明提供程序的证明读取者角色，请按照[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)中的常规说明进行操作。 处于“添加角色分配”窗格中时：

1. 在“角色”下拉列表中，选择“证明读取者”角色。
1. 在“选择”字段中，输入 Azure SQL 服务器的名称以搜索它。

相关示例请参阅以下屏幕截图。

![证明读取者角色分配](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> 若要使服务器显示在“添加角色分配”窗格中，服务器必须分配有 Azure AD 标识 - 请参阅[要求](#requirements)。

### <a name="use-powershell-to-assign-permission"></a>使用 PowerShell 分配权限

1. 查找 Azure SQL 逻辑服务器。

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. 将服务器分配给包含证明提供程序的资源组的证明读取者角色。

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

有关详细信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples)。

## <a name="next-steps"></a>后续步骤

- [管理具有安全 enclave 的 Always Encrypted 的密钥](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)
