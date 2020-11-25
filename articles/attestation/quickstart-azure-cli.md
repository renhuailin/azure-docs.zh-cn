---
title: 使用 Azure CLI 设置 Azure 证明
description: 如何使用 Azure CLI 设置和配置证明提供程序。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5ffcd56be1ce7427697cd3a75bd7c4232cc3b8a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663967"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>快速入门：使用 Azure CLI 设置 Azure 证明

通过使用 Azure CLI 设置证明，开始使用 Azure 证明。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>入门

1. 使用以下命令登录到 Azure：

   ```azurecli
   az login
   ```

1. 如果需要，请切换到 Azure 证明的订阅：

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. 在订阅中，使用 [az provider register](/cli/azure/provider#az_provider_register) 命令注册 Microsoft.Attestation 资源提供程序：

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   有关 Azure 资源提供程序以及如何配置和管理它们的详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

   > [!NOTE]
   > 仅需为订阅注册一次资源提供程序。

1. 为证明提供程序创建资源组。 可将其他 Azure 资源（包括具有客户端应用程序实例的虚拟机）放在同一资源组中。 运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组或使用现有资源组：

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>创建和管理证明提供程序

按照此过程创建和管理证明提供程序。

1. 运行 [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) 命令创建证明提供程序：

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   “--certs-input-path”参数指定一组受信任的签名密钥。 如果为此参数指定一个文件名，只能使用已签名 JWT 格式的策略来配置证明提供程序。 否则，可以以文本或未签名 JWT 格式配置策略。 有关 JWT 的信息，请参阅[基本概念](basic-concepts.md)。 有关证书示例，请参阅[证明策略签名者证书的示例](policy-signer-examples.md)。

1. 运行 [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) 命令检索证明提供程序属性（例如 status 和 AttestURI）：

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   此命令显示类似于以下输出的值：

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

可使用 [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) 命令删除证明提供程序：

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>策略管理

为了管理策略，Azure AD 用户需要对 `Actions` 具有以下权限：

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

可以通过 `Owner`（通配符权限）、`Contributor`（通配符权限）或 `Attestation Contributor`（仅适用于 Azure 证明的特定权限）等角色将这些权限分配给 Azure AD 用户。  

为了读取策略，Azure AD 用户需要对 `Actions` 具有以下权限：

- `Microsoft.Attestation/attestationProviders/attestation/read`

可以通过 `Reader`（通配符权限）或 `Attestation Reader`（仅适用于 Azure 证明的特定权限）等角色将此权限分配给 Azure AD 用户。

使用此处所述的命令可为证明提供程序提供策略管理，一次一个 TEE。

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) 命令返回指定 TEE 的当前策略：

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> 此命令以文本和 JWT 格式显示策略。

以下是支持的 TEE 类型：

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

使用 [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) 命令为指定的 TEE 设置新策略。

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

JWT 格式的证明策略必须包含名为 `AttestationPolicy` 的声明。 必须用与任何现有策略签名者证书相对应的密钥对已签名的策略进行签名。

有关策略示例，请参阅[证明策略的示例](policy-examples.md)。

[az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) 命令为指定的 TEE 设置新策略。

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>策略签名者证书管理

使用以下命令管理证明提供程序的策略签名者证书：

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

策略签名者证书是带有名为 `maa-policyCertificate` 的声明的签名 JWT。 声明的值为 JWK，其中包含要添加的受信任签名密钥。 必须用与任何现有策略签名者证书相对应的私钥对 JWT 进行签名。 有关 JWT 和 JWK 的信息，请参阅[基本概念](basic-concepts.md)。

策略签名者证书的所有语义操作都必须在 Azure CLI 之外完成。 就 Azure CLI 而言，它是一个简单的字符串。

有关证书示例，请参阅[证明策略签名者证书的示例](policy-signer-examples.md)。

## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用代码示例通过 SGX enclave 实现证明](/samples/browse/?expanded=azure&terms=attestation)
