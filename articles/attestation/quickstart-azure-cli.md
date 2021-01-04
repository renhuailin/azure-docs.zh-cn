---
title: 使用 Azure CLI 设置 Azure 证明
description: 如何使用 Azure CLI 设置和配置证明提供程序。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: fb8b0f12844ce1057bd3cfc4716a32ee64ec5586
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937213"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>快速入门：使用 Azure CLI 设置 Azure 证明

通过使用 Azure CLI 设置证明，开始使用 Azure 证明。

## <a name="get-started"></a>入门

1. 使用以下 CLI 命令安装此扩展

   ```azurecli
   az extension add --name attestation
   ```
   
1. 检查版本

   ```azurecli
   az extension show --name attestation --query version
   ```

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

可使用以下命令创建和管理证明提供程序：

1. 运行 [az attestation create](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_create) 命令创建证明提供程序：

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. 运行 [az attestation show](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_show) 命令检索证明提供程序属性（例如 status 和 AttestURI）：

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
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

可使用 [az attestation delete](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_delete) 命令删除证明提供程序：

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>策略管理

使用此处所述的命令可为证明提供程序（一次一个证明类型）提供策略管理。

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_show) 命令返回指定 TEE 的当前策略：

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> 此命令以文本和 JWT 格式显示策略。

以下是支持的 TEE 类型：

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

使用 [az attestation policy set](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_set) 命令为指定的证明类型设置新策略。

若要使用文件路径以文本格式为给定类型的证明设置策略，请运行以下命令：

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

若要使用文件路径以 JWT 格式为给定类型的证明设置策略，请运行以下命令：

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用代码示例通过 SGX enclave 实现证明](/samples/browse/?expanded=azure&terms=attestation)
