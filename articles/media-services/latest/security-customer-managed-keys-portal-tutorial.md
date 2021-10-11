---
title: 使用客户管理的密钥或 BYOK 门户
description: 本教程使用 Azure 门户在 Azure 媒体服务存储帐户中启用客户管理的密钥或创建自己的密钥 (BYOK) 服务。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 1a5b4c8e387085b8fee6c6e82ae765a186f43e87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356677"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>教程：使用 Azure 门户将客户管理的密钥或 BYOK 与媒体服务配合使用

借助 2020-05-01 或更高版本的 API，可将客户管理的 RSA 密钥与具有系统托管标识的 Azure 媒体服务帐户配合使用。本教程包含 Azure 门户中的步骤。

使用的服务是：

- Azure 存储
- Azure Key Vault
- Azure 媒体服务

本教程介绍如何使用 Azure 门户来完成以下操作：

> [!div class="checklist"]
> - 创建资源组。
> - 创建包含系统托管标识的存储帐户。
> - 创建包含系统托管标识的媒体服务帐户。
> - 创建用于存储客户管理的 RSA 密钥的密钥保管库。

## <a name="prerequisites"></a>先决条件

Azure 订阅。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free/)。

## <a name="system-managed-keys"></a>系统管理的密钥

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> 对于以下存储帐户创建步骤，需要在“高级设置”中选择“系统管理的密钥”选项。

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> 对于以下存储加密步骤，需要选择“客户管理的密钥”选项。

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>更改密钥

媒体服务自动检测密钥何时发生了更改。 可选：若要对此进程进行测试，请为同一密钥创建另一个密钥版本。 媒体服务应该会检测到此密钥已发生更改。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用所创建的资源，并且不想继续付费，请删除这些资源。

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何：
> [!div class="nextstepaction"]
> [基于 URL 对远程文件进行编码并使用 REST 流式传输视频](stream-files-tutorial-with-rest.md)
