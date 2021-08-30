---
title: 使用 Azure 证明为 Always Encrypted 配置证明
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
ms.date: 07/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6a27acf96b42a4a963b88e281fd692a91616b7e4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727194"
---
# <a name="configure-attestation-for-always-encrypted-using-azure-attestation"></a>使用 Azure 证明为 Always Encrypted 配置证明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Microsoft Azure 证明](../../attestation/overview.md)是证明受信任执行环境 (TEE) 的解决方案，包括 Intel Software Guard Extensions (Intel SGX) enclave。 

若要在 Azure SQL 数据库中使用 Azure 证明来证明用于[具有安全 enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 的 Intel SGX enclave，需执行以下操作：

1. 创建[证明提供程序](../../attestation/basic-concepts.md#attestation-provider)，并使用建议证明策略进行配置。

2. 确定证明 URL 并与应用程序管理员共享。

> [!NOTE]
> 配置证明是证明管理员的职责。 请参阅[配置 SGX enclave 和证明时的角色和职责](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

## <a name="create-and-configure-an-attestation-provider"></a>创建和配置证明提供程序

[证明提供程序](../../attestation/basic-concepts.md#attestation-provider)是 Azure 证明中的一种资源，可按照[证明策略](../../attestation/basic-concepts.md#attestation-request)评估[证明请求](../../attestation/basic-concepts.md#attestation-request)并颁发[证明令牌](../../attestation/basic-concepts.md#attestation-token)。 

证明策略使用[声明规则语法](../../attestation/claim-rule-grammar.md)进行指定。

> [!IMPORTANT]
> 证明提供程序使用 Intel SGX enclave 的默认策略进行创建，该策略不会验证在 enclave 内运行的代码。 Microsoft 强烈建议为具有安全 enclave 的 Always Encrypted 设置以下建议策略，而不是使用默认策略。

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
> Microsoft 可能需要轮换用于对 Always Encrypted enclave 二进制文件签名的密钥，这种情况应该极少发生。 在使用新密钥签名的新版 enclave 二进制文件部署到 Azure SQL 数据库之前，本文将更新以提供新的建议证明策略，并说明应如何更新证明提供程序中的策略以确保应用程序继续不间断地工作。

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

配置证明策略后，需要向在 Azure SQL 数据库中使用具有安全 enclave 的 Always Encrypted 的应用程序管理员共享证明 URL。 证明 URL 是包含证明策略的证明提供程序的 `Attest URI`，如下所示：`https://MyAttestationProvider.wus.attest.azure.net`。

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>使用 Azure 门户确定证明 URL

在证明提供程序的“概述”窗格中，将 `Attest URI` 属性的值复制到剪贴板。 

### <a name="use-powershell-to-determine-the-attestation-url"></a>使用 PowerShell 确定证明 URL

使用 `Get-AzAttestation` cmdlet 来检索证明提供程序属性，包括 AttestURI。

```powershell
Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName
```

有关详细信息，请参阅[创建并管理证明提供程序](../../attestation/quickstart-powershell.md#create-and-manage-an-attestation-provider)。

## <a name="next-steps"></a>后续步骤

- [管理具有安全 enclave 的 Always Encrypted 的密钥](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)
