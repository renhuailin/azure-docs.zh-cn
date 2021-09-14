---
title: 以本地共享（容器）形式装载 Azure 存储
description: 了解如何在 Azure 应用服务中向容器化应用附加自定义网络共享。 在应用之间共享文件、远程管理静态内容和本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 09/02/2021
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 77dc45d71a4a9706dd645289dd5839ee97c17314
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123472013"
---
# <a name="mount-azure-storage-as-a-local-share-in-a-container-app-in-app-service"></a>将 Azure 存储作为本地共享装载到应用服务中的容器应用

::: zone pivot="container-windows"

> [!NOTE]
>应用服务 Windows 容器中的 Azure 存储处于预览阶段，不支持用于生产方案  。

本指南介绍了如何在应用服务中将 Azure 存储文件作为网络共享装载到 Windows 容器中。 仅支持 [Azure 文件存储共享](../storage/files/storage-how-to-use-files-cli.md)和[高级文件共享](../storage/files/storage-how-to-create-file-share.md)。 自定义装载存储的优势包括：

::: zone-end

::: zone pivot="container-linux"

本指南介绍如何在应用服务中将 Azure 存储作为网络共享装载到内置 linux 容器或自定义 Linux 容器中。 自定义装载存储的优势包括：

::: zone-end

- 为应用服务应用配置永久性存储，并单独管理存储。
- 使静态内容（如视频和图像）可随时用于应用服务应用。 
- 将应用程序日志文件写入 Azure 文件共享，或将较旧的应用程序日志存档到 Azure 文件共享。  
- 跨多个应用或与其他 Azure 服务共享内容。

::: zone pivot="container-windows"

Windows 容器支持以下功能：

- 使用[专用链接](../storage/common/storage-private-endpoints.md)对存储帐户进行安全访问（当使用 [VNET 集成](web-sites-integrate-with-vnet.md)时）。 [服务终结点](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)支持当前不可用。
- Azure 文件存储（读取/写入）。
- 每个应用最多 5 个装入点。
- 驱动器号分配（`C:` 到 `Z:`）。

::: zone-end

::: zone pivot="container-linux"

Linux 容器支持以下功能：

- 使用[服务终结点](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)和[专用链接](../storage/common/storage-private-endpoints.md)对存储帐户进行安全访问（当使用 [VNET 集成](web-sites-integrate-with-vnet.md)时）。
- Azure 文件存储（读取/写入）。
- Azure Blob（只读）。
- 每个应用最多 5 个装入点。

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
> Azure 存储是适用于应用服务且单独计费的非默认存储，不包含在应用服务中。
>

## <a name="limitations"></a>限制

::: zone pivot="container-windows"

- 本机 Windows（非容器化）应用不支持存储装载。
- Azure Blob 不受支持。
- 仅通过[专用终结点](../storage/common/storage-private-endpoints.md)支持[存储防火墙](../storage/common/storage-network-security.md)（当使用 [VNET 集成](web-sites-integrate-with-vnet.md)时）。 当装载的 Azure 存储帐户使用专用终结点时，自定义 DNS 支持当前不可用。
- 不支持（使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）对已装载存储的 FTP/FTPS 访问。
- 不支持将 `[C-Z]:\`、`[C-Z]:\home`、`/` 和 `/home` 映射到自定义装载的存储。
- 在[部署槽位](deploy-staging-slots.md)创建期间，不能将存储装载与克隆设置选项一起使用。
- 当你[备份应用](manage-backup.md)时，存储装载并不会备份。 请务必依据最佳做法来备份 Azure 存储帐户。 

::: zone-end

::: zone pivot="container-linux"

- [存储防火墙](../storage/common/storage-network-security.md)仅支持[服务终结点](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)和[专用终结点](../storage/common/storage-private-endpoints.md)（当使用 [VNET 集成](web-sites-integrate-with-vnet.md)时）。 当装载的 Azure 存储帐户使用专用终结点时，自定义 DNS 支持当前不可用。
- 不支持（使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）对自定义装载存储的 FTP/FTPS 访问。
- Azure CLI、Azure PowerShell 和 Azure SDK 支持均处于预览阶段。
- 不支持将 `/` 或 `/home` 映射到自定义装载的存储。
- 在[部署槽位](deploy-staging-slots.md)创建期间，不能将存储装载与克隆设置选项一起使用。
- 当你[备份应用](manage-backup.md)时，存储装载并不会备份。 请务必依据最佳做法来备份 Azure 存储帐户。 

::: zone-end

::: zone pivot="container-windows"
## <a name="mount-storage-to-windows-container"></a>将存储装载到 Windows 容器
::: zone-end
::: zone pivot="container-linux"
## <a name="mount-storage-to-linux-container"></a>将存储装载到 Linux 容器
::: zone-end

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用。
1. 从左侧导航栏中，单击“配置” > “路径映射” > “新建 Azure 存储装载”  。 
1. 按照下表配置存储装载。 完成后，单击 **“确定”** 。

    ::: zone pivot="container-windows"
    | 设置 | 说明 |
    |-|-|
    | **名称** | 装载配置的名称。 不允许空格。 |
    | 配置选项 | 如果存储帐户未使用[专用终结点](../storage/common/storage-private-endpoints.md)，则选择“基础”。 否则，选择“高级”。 |
    | **存储帐户** | Azure 存储帐户。 它必须包含一个 Azure 文件存储共享。 |
    | 共享名 | 要装载的文件存储共享。 |
    | 访问密钥（仅高级） | 存储帐户的[访问密钥](../storage/common/storage-account-keys-manage.md)。 |
    | “装载路径” | 要装载到 Azure 存储的 Windows 容器中的目录。 请勿使用根目录（`[C-Z]:\` 或 `/`）或 `home` 目录（`[C-Z]:\home` 或 `/home`）。|
    ::: zone-end
    ::: zone pivot="container-linux"
    | 设置 | 说明 |
    |-|-|
    | **名称** | 装载配置的名称。 不允许空格。 |
    | 配置选项 | 如果存储帐户未使用[服务终结点](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)或[专用终结点](../storage/common/storage-private-endpoints.md)，则选择“基本”。 否则，选择“高级”。 |
    | **存储帐户** | Azure 存储帐户。 |
    | **存储类型** | 根据要装载的存储选择类型。 Azure Blob 仅支持只读访问。 |
    | “存储容器”或“共享名”  | 要装载的文件共享或 Blob 容器。 |
    | 访问密钥（仅高级） | 存储帐户的[访问密钥](../storage/common/storage-account-keys-manage.md)。 |
    | “装载路径” | 要装载到 Azure 存储的 Linux 容器中的目录。 不要使用 `/` 或 `/home`。|
    ::: zone-end

    > [!CAUTION]
    > 容器的装载路径中指定的目录应为空。 当装载 Azure 存储时，存储在该目录中的任何内容都会被删除（例如，如果在 `/home` 下指定一个目录）。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
    >
    
# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 命令。 例如：

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

::: zone pivot="container-windows"
- `--storage-type` 必须是适用于 Windows 容器的 `AzureFiles`。 
- `mount-path-directory` 的格式必须为无驱动器号的 `/path/to/dir` 或 `[C-Z]:\path\to\dir`。 请勿使用根目录（`[C-Z]:\` 或 `/`）或 `home` 目录（`[C-Z]:\home` 或 `/home`）。
::: zone-end
::: zone pivot="container-linux"
- `--storage-type` 可以是 `AzureBlob` 或 `AzureFiles`。 `AzureBlob` 为只读。
- `--mount-path` 为要装载到 Azure 存储的 Linux 容器中的目录。 请勿使用 `/`（根目录）。
::: zone-end

运行以下命令验证存储是否装载：

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

> [!CAUTION]
> 容器的 `--mount-path` 中指定的目录应为空。 当装载 Azure 存储时，存储在该目录中的任何内容都会被删除（例如，如果在 `/home` 下指定一个目录）。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
>

运行以下命令，验证配置：

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

---

> [!NOTE]
> 添加、编辑或删除存储装载会导致该应用重新启动。 

::: zone pivot="container-linux"

## <a name="test-the-mounted-storage"></a>对装载的存储进行测试

要验证是否已成功为应用装载 Azure 存储，请执行以下操作：

1. 在容器中[打开 SSH 会话](configure-linux-open-ssh-session.md)。
1. 在 SSH 终端中执行以下命令：

    ```bash
    df –h 
    ```
1. 检查存储共享是否已装载。 如果没有，则存储共享装载出现问题。
1. 通过以下命令检查存储装载的延迟或一般可达性：

    ```bash
    tcpping Storageaccount.file.core.windows.net 
    ```

::: zone-end

## <a name="best-practices"></a>最佳实践

- 若要避免与延迟相关的潜在问题，请将应用和 Azure 存储帐户放置在同一 Azure 区域中。 但请注意，如果应用和 Azure 存储帐户位于同一 Azure 区域，并且在 [Azure 存储防火墙配置](../storage/common/storage-network-security.md)中从应用服务 IP 地址授予访问权限，则不会遵循这些 IP 限制。
::: zone pivot="container-windows"
- 容器应用中的装载目录应为空。 当装载 Azure 存储时，存储在该路径下的任何内容都会被删除。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
::: zone-end
::: zone pivot="container-linux"
- 容器应用中的装载目录应为空。 当装载 Azure 存储时，存储在该路径下的任何内容都会被删除（例如，如果在 `/home` 下指定一个目录）。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。

- 建议不要将存储装载到 `/home`，因为这可能会导致应用的性能瓶颈。 
::: zone-end
- 在 Azure 存储帐户中，避免[重新生成用于在应用中装载存储的访问密钥](../storage/common/storage-account-keys-manage.md)。 存储帐户包含两个不同的密钥。 使用分步方法，以确保重新生成密钥时，存储装载仍可用于应用。 例如，假设你在应用中使用了“密钥 1”来配置存储装载：

    1. 重新生成“密钥 2”。 
    1. 在存储装载配置中，更新访问密钥以使用重新生成的“密钥 2”。
    1. 重新生成“密钥 1”。

- 如果删除 Azure 存储帐户、容器或共享，请在应用中删除相应的存储装载配置，以避免错误发生。 

- 装载的 Azure 存储帐户可以是标准或高级性能层。 根据应用容量和吞吐量要求，为存储帐户选择适当的性能层。 查看存储类型对应的可伸缩性和性能目标：

    - [对于文件存储](../storage/files/storage-files-scale-targets.md)（Windows 和 Linux 容器）
    - [对于 Blob](../storage/blobs/scalability-targets.md)（仅 Linux 容器）

- 如果应用[扩展到多个实例](../azure-monitor/autoscale/autoscale-get-started.md)，则所有这些实例均连接到相同的已装载的 Azure 存储帐户。 若要避免性能瓶颈和吞吐量问题，请为存储帐户选择适当的性能层。  

- 建议不要将存储装载用于本地数据库（例如 SQLite）或依赖于文件句柄和锁定的任何其他应用程序和组件。 

- 在应用中使用 Azure 存储[专用终结点](../storage/common/storage-private-endpoints.md)时，需要设置以下两个应用设置：

    - `WEBSITE_DNS_SERVER` = `168.63.129.16`
    - `WEBSITE_VNET_ROUTE_ALL` = `1`

- 如果[启动存储故障转移](../storage/common/storage-initiate-account-failover.md)，并且存储帐户已装载到应用，则在重新启动应用或删除并添加 Azure 存储装载之前，装载将无法连接。 

## <a name="next-steps"></a>后续步骤

::: zone pivot="container-windows"

- [使用自定义容器将自定义软件迁移到 Azure 应用服务](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [配置自定义容器](configure-custom-container.md?pivots=platform-linux)。

::: zone-end