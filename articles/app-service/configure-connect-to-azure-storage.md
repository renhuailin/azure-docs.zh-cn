---
title: 添加 Azure 存储（容器）
description: 了解如何在 Azure 应用服务中向容器化应用附加自定义网络共享。 在应用之间共享文件、远程管理静态内容和本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: c3e5ca30b7f0f00b6d647f5fdaeb772900996adc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720233"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>在应用服务中访问 Azure 存储（预览）作为容器中的网络共享

::: zone pivot="container-windows"

本指南介绍了如何在应用服务中将 Azure 存储文件作为网络共享附加到 Windows 容器。 仅支持 [Azure 文件存储共享](../storage/files/storage-how-to-use-files-cli.md)和[高级文件共享](../storage/files/storage-how-to-create-file-share.md)。 优势包括内容受保护，内容可移植，可以访问多个应用以及使用多种传输方法。

> [!NOTE]
>应用服务中的 Azure 存储处于预览阶段，不支持用于生产方案。

::: zone-end

::: zone pivot="container-linux"

本指南介绍了如何在应用服务中将 Azure 存储附加到 Linux 容器。 优势包括内容安全、内容可移植性、永久存储、访问多个应用和多种传输方法。

> [!NOTE]
>对于 Linux 上的应用服务和用于容器的 Web 应用，应用服务中的 Azure 存储处于预览阶段。 不支持将它用于生产方案。

::: zone-end

## <a name="prerequisites"></a>先决条件

::: zone pivot="container-windows"

- [Azure 应用服务中的现有 Windows 容器应用](quickstart-custom-container.md)
- [创建 Azure 文件存储共享](../storage/files/storage-how-to-use-files-cli.md)
- [将文件上传到 Azure 文件存储共享](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- 现有 [Linux 上的应用服务应用](index.yml)。
- [Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure 文件存储共享和目录](../storage/files/storage-how-to-use-files-cli.md)。

::: zone-end

> [!NOTE]
> Azure 文件存储是单独计费的非默认存储，不包含在 Web 应用中。 由于基础结构的限制，它不支持使用防火墙配置。
>

## <a name="limitations"></a>限制

::: zone pivot="container-windows"

- 目前，不支持将应用服务中的 Azure 存储用于自带代码方案（非容器化 Windows 应用）。
- 由于基础结构的限制，应用服务中的 Azure 存储不支持使用存储防火墙配置。
- 利用应用服务中的 Azure 存储，可以为每个应用指定最多 5 个装入点。
- 装载到应用的 Azure 存储不可通过应用服务 FTP/FTP 终结点访问。 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

::: zone-end

::: zone pivot="container-linux"

- 应用服务中的 Azure 存储支持装载 Azure 文件存储容器（读/写）和 Azure Blob 容器（只读）
- 利用应用服务中的 Azure 存储，可以为每个应用指定最多 5 个装入点。
- 装载到应用的 Azure 存储不可通过应用服务 FTP/FTP 终结点访问。 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

::: zone-end

## <a name="link-storage-to-your-app"></a>将存储链接到应用

::: zone pivot="container-windows"

在创建了 [Azure 存储帐户、文件共享和目录](#prerequisites)后，现在可以为应用配置 Azure 存储。

若要将 Azure 文件存储共享装载到应用服务应用中的目录，请使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 命令。 存储类型必须为 AzureFiles。

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

对于任何其他要链接到 Azure 文件存储共享的目录，你都应该这样做。

::: zone-end

::: zone pivot="container-linux"

在创建了 [Azure 存储帐户、文件共享和目录](#prerequisites)后，现在可以为应用配置 Azure 存储。

若要将存储帐户装载到你的应用服务应用中的某个目录，请使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 命令。 存储类型可以是 AzureBlob 或 AzureFiles。 此示例使用 AzureFiles。 装载路径设置对应于要装载到 Azure 存储的容器中的文件夹。 如果设置为“/”，则会将整个容器装载到 Azure 存储中。


> [!CAUTION]
> Web 应用中被指定为装载路径的目录应是空的。 当添加外部装载时，存储在此目录中的任何内容都将被删除。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

应当对要链接到存储帐户的任何其他目录执行此操作。

::: zone-end

## <a name="verify-linked-storage"></a>验证链接存储

在共享链接到应用后，可以通过运行下面的命令来验证这一点：

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>后续步骤

::: zone pivot="container-windows"

- [使用自定义容器将自定义软件迁移到 Azure 应用服务](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [配置自定义容器](configure-custom-container.md?pivots=platform-linux)。

::: zone-end