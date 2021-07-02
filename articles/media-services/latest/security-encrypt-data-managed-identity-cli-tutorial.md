---
title: 使用 Key Vault 密钥将数据加密到媒体服务帐户
description: 如果希望媒体服务使用 Key Vault 中的密钥来加密数据，必须向媒体服务帐户授予访问 Key Vault 的权限。 按照下列步骤为媒体服务帐户创建一个托管标识，然后使用媒体服务 CLI 向该标识授予访问其 Key Vault 的权限。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: inhenkel
ms.openlocfilehash: 20b0ec0025fcac72005eb8b2343ede0ef9e5d9b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464315"
---
# <a name="tutorial-use-a-key-vault-key-to-encrypt-data-into-a-media-services-account"></a>教程：使用 Key Vault 密钥将数据加密到媒体服务帐户

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

如果希望媒体服务使用 Key Vault 中的密钥来加密数据，必须向媒体服务帐户授予访问 Key Vault 的权限。 按照下列步骤为媒体服务帐户创建一个托管标识，然后使用媒体服务 CLI 向该标识授予访问 Key Vault 的权限。

:::image type="content" source="media/diagrams/managed-identities-scenario-keyvault-media-services-account.svg" alt-text="媒体服务帐户通过托管标识使用 Key Vault":::



本教程使用媒体服务 API 2020-05-01 版。

## <a name="sign-in-to-azure"></a>登录 Azure

要使用本文中的任何命令，首先必须登录到要使用的订阅。

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>设置订阅

使用此命令设置要使用的订阅。

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="resource-names"></a>资源名称

在开始之前，确定将要创建的资源的名称。  这些名称应该容易被识别成一组，尤其是如果你在完成测试后不打算使用它们。 许多资源类型的命名规则是不同的，因此最好始终采用全部小写的形式。 例如，“mediatest1rg”表示资源组名称，而“mediatest1stor”表示存储帐户名称。 对本文中的每个步骤使用相同的名称。

你将看到以下命令中引用了这些名称。  所需资源名称包括：

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-keyvault-name
- your-key-name
- your-region

> [!NOTE]
> 上述连字符仅用于分隔引导词。 由于 Azure 服务中的命名资源不一致，因此在命名资源时请勿使用连字符。
> 此外，也不用创建区域名称。  区域名称由 Azure 确定。

### <a name="list-azure-regions"></a>列出 Azure 区域

如果不确定要使用的实际区域名称，请使用此命令获取列表：

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>序列

以下每个步骤都是按特定顺序完成的，因为你在序列的下一步中使用了来自 JSON 响应的一个或多个值。

## <a name="create-a-resource-group"></a>创建资源组

你将创建的资源必须属于资源组。 首先创建资源组。 你将在媒体服务帐户创建步骤和后续步骤中使用 `your-resource-group-name`。

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>创建存储帐户

你将创建的媒体服务帐户必须具有与之关联的存储帐户。 首先为媒体服务帐户创建存储帐户。 你将在后续步骤中使用 `your-storage-account-name`。

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account-with-a-service-principal-managed-identity"></a>创建包含服务主体（托管标识）的媒体服务帐户

现在，创建包含服务主体（也称为“托管标识”）的媒体服务帐户。

> [!IMPORTANT]
> 请务必记住在命令中使用 --mi 标志。  否则，你将无法找到 `principalId` 供稍后步骤使用。

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

创建 Key Vault。  Key Vault 用于加密媒体数据。 你将使用 `your-keyvault-name` 来创建密钥并供后续步骤使用。

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-key-vault-cli.md)]

## <a name="grant-the-media-services-system-assigned-managed-identity-access-to-the-key-vault"></a>向媒体服务系统分配的托管标识授予访问 Key Vault 的权限

向媒体服务托管标识授予访问 Key Vault 的权限。 有两个命令：

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>获取（显示）媒体服务帐户的托管标识

下面的第一个命令显示媒体服务帐户的托管标识，它是命令返回的 JSON 中列出的 `principalId`。

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="set-the-key-vault-policy"></a>设置 Key Vault 策略

第二个命令向主体 ID 授予访问 Key Vault 的权限。 将 `object-id` 设置为你在上一步中获得的 `principalId` 的值。

[!INCLUDE [Set the Key Vault policy with the CLI](./includes/task-set-key-vault-policy-cli.md)]

### <a name="set-media-services-to-use-the-key-from-key-vault"></a>将媒体服务设置为使用 Key Vault 中的密钥

将媒体服务设置为使用你创建的密钥。 `key-identifier` 属性的值来自创建密钥时的输出。 由于传播访问控制更改需要时间，因此该命令可能会失败。 如果失败，请在几分钟后重试。

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-set-encryption-cli.md)]

## <a name="validation"></a>验证

若要验证帐户是否使用客户管理的密钥进行加密，请查看帐户加密属性：

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-encryption-cli.md)]

`type` 属性应会显示 `CustomerKey`，并且 `currentKeyIdentifier` 会设置为客户的 Key Vault 中的密钥路径。

## <a name="clean-up-resources"></a>清理资源

如果不打算使用已创建的资源，请删除资源组。

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
