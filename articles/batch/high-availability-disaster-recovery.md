---
title: 高可用性和灾难恢复
description: 了解如何针对区域性中断问题设计 Batch 应用程序。
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831000"
---
# <a name="design-your-batch-application-for-high-availability"></a>设计 Batch 应用程序以实现高可用性

Azure Batch 在所有 Azure 区域中均可用，但在创建批处理帐户时，它必须与一个特定的区域关联。 然后，Batch 帐户的所有操作会应用到该区域。 例如，池和关联的虚拟机 (VM) 在 Batch 帐户所在的区域中创建。

设计使用 Batch 的应用程序时，必须考虑到 Batch 在某个区域不可用的可能性。 可能会遇到这样的情况：整体区域出现问题、区域中的整个批处理服务或特定的批处理帐户出现问题。

如果使用 Batch 的应用程序或解决方案必须始终可用，则应将其设计为可以故障转移到另一区域，或者始终在两个或更多个区域之间拆分工作负荷。 两种方法都需要至少两个 Batch 帐户，每个帐户位于不同的区域中。

## <a name="multiple-batch-accounts-in-multiple-regions"></a>多个 Batch 帐户位于多个区域中

如果在多个区域中使用多个批处理帐户，则如果一个区域中的批处理帐户不可用，应用程序将继续运行。 如果你的应用程序需要高度可用，则拥有多个帐户尤其重要。

在某些情况下，应用程序可能会有意使用两个或多个区域。 例如，在需要的容量相当大时，则可能需要使用多个区域，以应对出现大型应用程序或者将来出现增长的情况。 这些应用程序还需要多个批处理帐户， (每个区域使用) 。

## <a name="design-considerations-for-providing-failover"></a>有关如何提供故障转移的设计注意事项

提供故障转移到备用区域的能力时，需要考虑解决方案中的所有组件;只需使用第二个 Batch 帐户就够了。 例如，在大多数 Batch 应用程序中，需要一个 Azure 存储帐户，而存储帐户和 Batch 帐户需要位于同一区域中，这样才能获得可以接受的性能。

设计可以故障转移的解决方案时，请注意以下几点：

- 在每个区域预先创建所有必需的帐户，例如 Batch 帐户和存储帐户。 创建帐户通常不收取任何费用，并且仅当使用帐户或存储数据时才会产生费用。
- 请确保提前为所有帐户设置适当的 [配额](batch-quota-limit.md) ，以便可以使用批处理帐户分配所需的内核数。
- 使用模板和/或脚本在区域中自动部署应用程序。
- 始终更新所有区域中的应用程序二进制文件和引用数据。 保持更新可确保将区域快速联机，不需等待文件的上传和部署。 例如，如果使用 Batch 应用程序包来存储和引用需安装在池节点中的自定义应用程序，则在生成新版应用程序后，应将其上传到每个 Batch 帐户并通过池配置对其进行引用（或者使新版本成为默认版本）。
- 在调用 Batch、存储和其他任何服务的应用程序中，可以轻松地将客户端或负载切换到不同区域。
- 在正常操作过程中，请考虑频繁切换到备用区域。 例如，在不同区域的两个部署中，每月切换到备用区域。

## <a name="next-steps"></a>后续步骤

- 详细了解如何通过 [Azure 门户](batch-account-create-portal.md)、[Azure CLI](./scripts/batch-cli-sample-create-account.md)、[PowerShell](batch-powershell-cmdlets-get-started.md) 或 [Batch 管理 API](batch-management-dotnet.md) 创建 Batch 帐户。
- 了解 [与 Batch 帐户关联的默认配额](batch-quota-limit.md) 以及如何提高配额。
