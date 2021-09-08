---
title: Azure Functions 的存储注意事项
description: 了解 Azure Functions 的要求和存储数据加密。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: ad9e7979eddac3fc102d9fddae68c230a7418762
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259562"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions 的存储注意事项

创建函数应用实例时，Azure Functions 需要 Azure 存储帐户。 函数应用可以使用以下存储服务：


|存储服务  | 函数用法  |
|---------|---------|
| [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)     | 维护绑定状态和函数密钥。  <br/>还由 [Durable Functions 中的任务中心](durable/durable-functions-task-hubs.md)使用。 |
| [Azure 文件](../storage/files/storage-files-introduction.md)  | 文件共享，用于存储和运行[消耗计划](consumption-plan.md)和[高级计划](functions-premium-plan.md)中的函数应用代码。 <br/>默认已设置 Azure 文件存储，但在某些情况下，你可以[创建不使用 Azure 文件存储的应用](#create-an-app-without-azure-files)。 |
| Azure 队列存储     | 由 [Durable Functions 中的任务中心](durable/durable-functions-task-hubs.md)使用。   |
| [Azure 表存储](../storage/tables/table-storage-overview.md)  |  由 [Durable Functions 中的任务中心](durable/durable-functions-task-hubs.md)使用。       |

> [!IMPORTANT]
> 使用消耗/高级托管计划时，函数代码和绑定配置文件存储在主存储帐户的 Azure 文件存储中。 删除主存储帐户时，此内容将随之删除且无法恢复。

## <a name="storage-account-requirements"></a>存储帐户要求

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 这是因为 Functions 依赖于 Azure 存储，执行管理触发器和记录函数执行等操作。 某些存储帐户不支持队列和表。 这些帐户包括仅 Blob 存储帐户和 Azure 高级存储。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#core-storage-services)。 

虽然可以将现有存储帐户用于函数应用，不过必须确保它满足这些要求。 可以保证通过 Azure 门户在函数应用创建流中创建的存储帐户满足这些存储帐户要求。 通过门户在创建函数应用的过程中选择现有存储帐户时，会筛选掉不受支持的帐户。 在此流中，只允许选择要创建的函数应用所在区域中的现有存储帐户。 若要了解详细信息，请参阅[存储帐户位置](#storage-account-location)。

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>存储帐户指导

每个 Function App 都需要存储帐户才能运行。 如果该帐户已删除，则你的函数应用将不会运行。 若要对存储相关问题进行故障排除，请参阅[如何对存储相关问题进行故障排除](functions-recover-storage-account.md)。 以下附加注意事项适用于函数应用使用的存储帐户。

### <a name="storage-account-location"></a>存储帐户位置

为了获得最佳性能，函数应用应使用同一区域中的存储帐户，从而减少延迟。 Azure 门户强制实施此最佳做法。 如果出于某种原因，需要使用不同于函数应用所在区域的区域中的存储帐户，则必须在门户外创建函数应用。 

### <a name="storage-account-connection-setting"></a>存储帐户连接设置

存储帐户连接在 [AzureWebJobsStorage 应用程序设置](./functions-app-settings.md#azurewebjobsstorage)中进行维护。 

重新生成存储密钥时，必须更新存储帐户连接字符串。 [在此处阅读有关存储密钥管理的详细信息](../storage/common/storage-account-create.md)。

### <a name="shared-storage-accounts"></a>共享存储帐户

多个函数应用可以共享同一个存储帐户，而不会出现任何问题。 例如，在 Visual Studio 中，可以使用 Azure 存储仿真器开发多个应用。 在这种情况下，仿真器的作用类似于单个存储帐户。 函数应用使用的同一个存储帐户也可用于存储应用程序数据。 但是在生产环境中，这种方法并不总是个好主意。

### <a name="optimize-storage-performance"></a>优化存储性能

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>存储数据加密

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>区域内数据驻留

当必须将所有客户数据保留在单个区域内时，与函数应用关联的存储帐户必须是具有[区域内冗余](../storage/common/storage-redundancy.md)的存储帐户。 区域内冗余存储帐户还必须与 [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) 一起使用。

其他由平台管理的客户数据只有托管在内部负载均衡的应用服务环境（简称 ASE）中时才会存储在该区域内。 若要了解详细信息，请参阅 [ASE 区域冗余](../app-service/environment/zone-redundancy.md#in-region-data-residency)。

## <a name="create-an-app-without-azure-files"></a>创建不使用 Azure 文件存储的应用

对于高级计划和非 Linux 消耗计划，默认已设置 Azure 文件存储作为大规模方案中的共享文件系统。 该文件系统供平台用来实现某些功能（例如日志流式处理），但它的主要用途是确保已部署的函数有效负载的一致性。 [使用外部包 URL 部署](./run-functions-from-deployment-package.md)应用时，应用内容将从单独的只读文件系统提供，因此可以按需忽略 Azure 文件存储。 在这种情况下，将提供可写文件系统，但不保证该文件系统与所有函数应用实例共享。

如果不使用 Azure 文件存储，则必须考虑以下要求：

* 必须从外部包 URL 进行部署
* 应用不能依赖于共享的可写文件系统
* 应用不能使用 Functions 运行时 v1
* 客户端（例如 Azure 门户）中的日志流式处理体验默认使用文件系统日志。 你应该改为依赖 Application Insights 日志。

如果正确考虑到了上述要求，则可以创建不使用 Azure 文件存储的应用。 在不指定 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 和 `WEBSITE_CONTENTSHARE` 应用程序设置的情况下创建函数应用。

## <a name="mount-file-shares"></a>装载文件共享

目前仅当在 Linux 上运行时，此功能才可用。 

可以将现有 Azure 文件共享装载到 Linux 函数应用。 通过将共享装载到 Linux 函数应用，可以利用现有的机器学习模型或函数中的其他数据。 可以使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 命令将现有共享装载到 Linux 函数应用。 

在此命令中，`share-name` 是现有 Azure 文件共享的名称，`custom-id` 可以是在装载到函数应用时唯一定义共享的任何字符串。 此外，`mount-path` 是在函数应用中用于访问共享的路径。 `mount-path` 必须采用 `/dir-name` 格式，不能以 `/home` 开头。

有关完整示例，请参阅[创建 Python 函数应用并装载 Azure 文件共享](scripts/functions-cli-mount-files-storage-linux.md)中的脚本。 

目前仅支持 `AzureFiles` 的 `storage-type`。 只能将五个共享装载到给定函数应用。 装载文件共享可能会至少将冷启动时间增加 200-300 毫秒，当存储帐户处于不同区域时甚至会更多。

装载的共享可供处于指定 `mount-path` 的函数代码使用。 例如，当 `mount-path` 为 `/path/to/mount` 时，可以通过文件系统 API 访问目标目录，如下面的 Python 示例所示：

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>后续步骤

详细了解 Azure Functions 托管选项。

> [!div class="nextstepaction"]
> [Azure Functions 的缩放和托管](functions-scale.md)
