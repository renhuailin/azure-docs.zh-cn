---
title: KeyVaultCertificateSelector UI 元素
description: 描述 Azure 门户的 KeyVault. KeyVaultCertificateSelector UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101215"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>KeyVault. KeyVaultCertificateSelector UI 元素

用于选择密钥保管库证书的控件。

## <a name="ui-sample"></a>UI 示例

用户向用户显示了选择可用证书的选项。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="KeyVault. KeyVaultCertificateSelector&quot;:::

选择 &quot; **选择证书** " 后，用户可以从密钥保管库中指定密钥保管库和证书。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="KeyVault. KeyVaultCertificateSelector&quot;:::

选择 &quot; **选择证书** ":::

控件将更新以显示选定的 key vault 和证书名称。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="KeyVault. KeyVaultCertificateSelector&quot;:::

选择 &quot; **选择证书** ":::

## <a name="schema"></a>架构

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
