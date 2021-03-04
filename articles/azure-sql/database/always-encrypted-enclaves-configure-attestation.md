---
title: 配置 Azure SQL 逻辑服务器的 Azure 证明
description: 在 Azure SQL 数据库中配置具有 secure enclaves 的 Azure 证明 for Always Encrypted。
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
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122572"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>配置 Azure SQL 逻辑服务器的 Azure 证明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库的具有安全 Enclave 的 Always Encrypted 目前提供公共预览版。

[Microsoft Azure 认证](../../attestation/overview.md) 是一种解决方案，适用于证明受信任的执行环境 (TEEs) ，包括 intel SGX) Enclaves (Intel 软件防护扩展。 

若要将 Azure 证明用于证明 Intel SGX enclaves，Always Encrypted 用于在 Azure SQL 数据库中使用 [安全 enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) ，需执行以下操作：

1. 创建一个 [证明提供者](../../attestation/basic-concepts.md#attestation-provider) ，并使用建议的证明策略进行配置。

2. 授予 Azure SQL 逻辑服务器对证明提供程序的访问权限。

> [!NOTE]
> 配置证明是证明管理员的责任。 请参阅 [配置 SGX enclaves 和证明时的角色和职责](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

## <a name="requirements"></a>要求

Azure SQL 逻辑服务器和证明提供程序必须属于同一个 Azure Active Directory 租户。 不支持跨租户交互。 

Azure SQL 逻辑服务器必须分配有 Azure AD 标识。 作为证明管理员，需要从该服务器的 Azure SQL 数据库管理员那里获取服务器的 Azure AD 标识。 你将使用该标识授予服务器对证明提供程序的访问权限。 

有关如何使用 PowerShell 和 Azure CLI 创建具有标识的服务器或向现有服务器分配标识的说明，请参阅向 [服务器分配 Azure AD 标识](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)。

## <a name="create-and-configure-an-attestation-provider"></a>创建并配置证明提供者

[证明提供程序](../../attestation/basic-concepts.md#attestation-provider)是 Azure 证明中的一种资源，用于评估针对[证明策略](../../attestation/basic-concepts.md#attestation-request)的[证明请求](../../attestation/basic-concepts.md#attestation-request)并颁发[证明令牌](../../attestation/basic-concepts.md#attestation-token)。 

使用 [声明规则语法](../../attestation/claim-rule-grammar.md)指定证明策略。

对于 Azure SQL 数据库中用于 Always Encrypted 的证明 Intel SGX enclaves，Microsoft 建议使用以下策略：

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

上述策略验证：

- Azure SQL 数据库中的 enclave 不支持调试。 
  > 已禁用或启用调试，可以加载 Enclaves。 调试支持旨在允许开发人员对 enclave 中运行的代码进行故障排除。 在生产系统中，调试可以让管理员检查 enclave 的内容，这将减少 enclave 提供的保护级别。 建议的策略禁用调试，以确保如果恶意管理员通过接管 enclave 计算机来尝试启用调试支持，则证明会失败。 
- Enclave 的产品 ID 与分配到安全 enclaves Always Encrypted 的产品 ID 匹配。
  > 每个 enclave 都有一个唯一的产品 ID，它将 enclave 与其他 enclaves 区分开来。 分配给 Always Encrypted enclave 的产品 ID 是4639。 
- 库的 (SVN) 的安全版本号大于0。
  > SVN 允许 Microsoft 响应 enclave 代码中标识的潜在安全问题。 如果安全问题发现和修复，Microsoft 将部署新版本的 enclave，新的 () SVN 增加。 上述建议策略将更新以反映新的 SVN。 通过更新策略来匹配推荐的策略，你可以确保在恶意管理员尝试加载较旧且不安全的 enclave 时，证明将会失败。
- Enclave 中的库已使用 Microsoft 签名密钥进行签名 (x mrsigner 声明的值是签名密钥) 的哈希。
  > 证明的主要目标之一是说服客户端，enclave 中运行的二进制文件是应该运行的二进制文件。 证明策略为此目的提供了两种机制。 一个是 **mrenclave** 声明，它是应在 enclave 中运行的二进制文件的哈希。 **Mrenclave** 的问题是，即使对代码进行了重大更改，二进制哈希也会更改，这使得难以处理在 enclave 中运行的代码。 因此，建议使用 **mrsigner**，它是用于对 enclave 二进制文件进行签名的密钥的哈希。 当 Microsoft revs enclave 时，只要签名密钥不变， **mrsigner** 就会保持不变。 这样，就可以在不中断客户应用程序的情况下部署更新的二进制文件。 

> [!IMPORTANT]
> 使用 Intel SGX enclaves 的默认策略创建证明提供者，它不会验证在 enclave 内运行的代码。 Microsoft 强烈建议你为安全 enclaves 的 Always Encrypted 设置上述建议策略，而不是使用默认策略。

有关如何使用认证策略创建证明提供者和配置的说明，请使用：

- [快速入门：使用 Azure 门户设置 Azure 证明](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > 在将证明策略配置 Azure 门户时，请将 "证明类型" 设置为 `SGX-IntelSDK` 。
- [快速入门：使用 Azure PowerShell 设置 Azure 证明](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > 在将证明策略配置 Azure PowerShell 时，请将 `Tee` 参数设置为 `SgxEnclave` 。
- [快速入门：使用 Azure CLI 设置 Azure 证明](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > 在将证明策略配置 Azure CLI 时，请将 `attestation-type` 参数设置为 `SGX-IntelSDK` 。

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>确定证明策略的证明 URL

配置证明策略后，需要在 Azure SQL 数据库中共享证明 URL，并引用策略、使用 Always Encrypted 与安全 enclaves 的应用程序的管理员。 应用程序管理员或/和应用程序用户将需要使用证明 URL 来配置其应用，以便他们可以运行使用安全 enclaves 的语句。

### <a name="use-powershell-to-determine-the-attestation-url"></a>使用 PowerShell 确定证明 URL

使用以下脚本确定证明 URL：

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>使用 Azure 门户确定证明 URL

1. 在证明提供者的 "概述" 窗格中，将 "证明 URI" 属性的值复制到 "剪贴板"。 证明 URI 应如下所示： `https://MyAttestationProvider.us.attest.azure.net` 。

2. 将以下内容追加到证明 URI： `/attest/SgxEnclave` 。 

生成的证明 URL 应如下所示： `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>向 Azure SQL 逻辑服务器授予对证明提供程序的访问权限

在证明工作流过程中，包含您的数据库的 Azure SQL 逻辑服务器将调用证明提供程序来提交证明请求。 要使 Azure SQL 逻辑服务器能够提交证明请求，服务器必须具有对 `Microsoft.Attestation/attestationProviders/attestation/read` 证明提供者的操作的权限。 授予该权限的建议方法是将服务器的 Azure AD 标识分配给证明提供者的证明读者角色或其包含的资源组。

### <a name="use-azure-portal-to-assign-permission"></a>使用 Azure 门户分配权限

若要将 Azure SQL server 的标识分配给证明提供者的证明读者角色，请遵循 [使用 Azure 门户分配 azure 角色](../../role-based-access-control/role-assignments-portal.md)中的常规说明。 在 " **添加角色分配** " 窗格中：

1. 在 " **角色** " 下拉端中，选择 " **证明读者** " 角色。
1. 在 " **选择** " 字段中，输入要搜索的 Azure SQL server 的名称。

有关示例，请参阅以下屏幕截图。

![证明读者角色分配](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> 要使服务器显示在 " **添加角色分配** " 窗格中，服务器必须分配有 Azure AD 标识-请参阅 [要求](#requirements)。

### <a name="use-powershell-to-assign-permission"></a>使用 PowerShell 分配权限

1. 查找 Azure SQL 逻辑服务器。

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. 为包含证明提供者的资源组将服务器分配到证明读者角色。

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

有关详细信息，请参阅 [使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples)。

## <a name="next-steps"></a>后续步骤

- [管理具有安全 enclave 的 Always Encrypted 的密钥](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>另请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)
