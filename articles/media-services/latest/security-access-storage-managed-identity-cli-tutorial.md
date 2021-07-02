---
title: 使用媒体服务托管标识访问存储
description: 如果要在存储帐户配置为阻止来自未知 IP 地址的请求时访问该存储帐户，必须向媒体服务帐户授予访问该存储帐户的权限。 按照下列步骤为媒体服务帐户创建一个托管标识，然后使用媒体服务 CLI 向该标识授予访问存储的权限。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 77eb737a48213701e424bbc8b1a49ead0c95e88c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464262"
---
# <a name="tutorial-access-storage-with-a-media-services-managed-identity"></a>教程：使用媒体服务托管标识访问存储

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

如果要在存储帐户配置为阻止来自未知 IP 地址的请求时访问该存储帐户，必须向媒体服务帐户授予访问该存储帐户的权限。 按照下列步骤为媒体服务帐户创建一个托管标识，然后使用媒体服务 CLI 向该标识授予访问存储的权限。

:::image type="content" source="media/diagrams/managed-identities-scenario-storage-permissions-media-services-account.svg" alt-text="媒体服务帐户使用托管标识来访问存储":::

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

## <a name="grant-the-media-services-managed-identity-access-to-the-storage-account"></a>向媒体服务托管标识授予访问存储帐户的权限

向媒体服务托管标识授予访问存储帐户的权限。 有三个命令：

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>获取（显示）媒体服务帐户的托管标识

下面的第一个命令显示媒体服务帐户的托管标识，它是命令返回的 JSON 中列出的 `principalId`。

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="create-the-storage-blob-contributor-role-assignment"></a>创建存储 Blob 参与者角色分配

[!INCLUDE [Create the Storage Blob Contributor role assignment](./includes/task-create-storage-blob-contributor-role-cli.md)]

### <a name="create-the-reader-role-assignment"></a>创建读取者角色分配

[!INCLUDE [Create the Reader role assignment](./includes/task-create-reader-role-cli.md)]

## <a name="use-the-managed-identity-to-access-the-storage-account"></a>使用托管标识访问存储帐户

[!INCLUDE [Use the Managed Identity to access the Storage account](./includes/task-set-storage-managed-identity-cli.md)]

## <a name="validation"></a>验证

若要验证帐户是否使用客户管理的密钥进行加密，请查看帐户加密属性：

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-managed-identity-cli.md)]

`storageAuthentication` 属性应显示“ManagedIdentity”。

若要进行其他验证，可检查 Azure 存储日志，查看每个请求使用了哪种身份验证方法。

## <a name="clean-up-resources"></a>清理资源

如果不打算使用已创建的资源，请删除资源组。

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
