---
title: 配置 Azure SQL 逻辑服务器的 Azure 证明
description: 在 Azure SQL 数据库中配置具有 secure enclaves 的 Azure 证明 for Always Encrypted。
keywords: 加密数据，sql 加密，数据库加密，敏感数据，Always Encrypted，安全 enclaves，SGX，证明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 51431bf0da9145e1b61da708942b675e4c3eea78
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733805"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>配置 Azure SQL 逻辑服务器的 Azure 证明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 对于 Azure SQL 数据库，具有 secure enclaves 的 Always Encrypted 当前提供 **公共预览版**。

[Microsoft Azure 认证](../../attestation/overview.md) 是一种解决方案，适用于证明受信任的执行环境 (TEEs) ，包括 intel SGX) Enclaves (Intel 软件防护扩展。 

若要将 Azure 证明用于证明 Intel SGX enclaves，Always Encrypted 用于在 Azure SQL 数据库中使用 [安全 enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) ，需执行以下操作：

1. 创建一个 [证明提供者](../../attestation/basic-concepts.md#attestation-provider) ，并使用建议的证明策略进行配置。

2. 向 Azure SQL 逻辑服务器授予对证明提供者的访问权限。

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

- Azure SQL 数据库中的 enclave 不支持调试 (这会降低 enclave 提供) 的保护级别。
- Enclave 中库的产品 ID 是分配给安全 enclaves (4639) Always Encrypted 的产品 ID。
- 库的版本 ID (svn) 大于0。
- Enclave 中的库已使用 Microsoft 签名密钥进行签名 (x mrsigner 声明的值是签名密钥) 的哈希。

> [!IMPORTANT]
> 使用 Intel SGX enclaves 的默认策略创建证明提供者，它不会验证在 enclave 内运行的代码。 Microsoft 强烈建议你为安全 enclaves 的 Always Encrypted 设置上述建议策略，而不是使用默认策略。

有关如何使用认证策略创建证明提供者和配置的说明，请使用：

- [快速入门：通过 Azure 门户设置 Azure 证明](../../attestation/quickstart-portal.md)
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

若要将 Azure SQL server 的标识分配给证明提供者的证明读者角色，请遵循 [使用 Azure 门户添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)中的常规说明。 在 " **添加角色分配** " 窗格中：

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

有关详细信息，请参阅[使用 Azure PowerShell 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md#add-role-assignment-examples)。

## <a name="next-steps"></a>后续步骤

- [管理具有安全 enclave 的 Always Encrypted 的密钥](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>另请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)
