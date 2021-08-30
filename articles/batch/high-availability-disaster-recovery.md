---
title: 高可用性和灾难恢复
description: 了解如何针对区域性中断问题设计 Batch 应用程序。
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: eb3d0cb218e607e38b7478b7cc7956963e450f46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730199"
---
# <a name="design-your-batch-application-for-high-availability"></a>设计 Batch 应用程序以实现高可用性

Azure Batch 在所有 Azure 区域提供，但在创建 Batch 帐户时，必须将其与特定区域相关联。 然后，Batch 帐户的所有操作会应用到该区域。 例如，池和关联的虚拟机 (VM) 在 Batch 帐户所在的区域中创建。

设计使用 Batch 的应用程序时，必须考虑到 Batch 在某个区域不可用的可能性。 可能会遇到罕见的情况，即：整个区域出现问题、区域中的整个 Batch 服务出现问题，或者特定 Batch 帐户出现问题。

如果使用 Batch 的应用程序或解决方案必须始终可用，则应将其设计为可以故障转移到另一区域，或者始终在两个或更多个区域之间拆分工作负荷。 两种方法都需要至少两个 Batch 帐户，每个帐户位于不同的区域中。

## <a name="multiple-batch-accounts-in-multiple-regions"></a>多个 Batch 帐户位于多个区域中

在不同的区域中使用多个 Batch 帐户时，应用程序可以在某个区域的某个 Batch 帐户变得不可用时继续运行。 如果应用程序需要高度可用，那么拥有多个帐户尤为重要。

在某些情况下，可以将应用程序设计为有意使用两个或多个区域。 例如，在需要的容量相当大时，则可能需要使用多个区域，以应对出现大型应用程序或者将来出现增长的情况。 这些应用程序还将需要多个 Batch 帐户（每个区域使用一个）。

## <a name="design-considerations-for-providing-failover"></a>有关如何提供故障转移的设计注意事项

当提供故障转移到备用区域的功能时，必须考虑解决方案中的所有组件；仅仅设置另一个 Batch 帐户是不够的。 例如，在大多数 Batch 应用程序中，需要一个 Azure 存储帐户，而存储帐户和 Batch 帐户需要位于同一区域中，这样才能获得可以接受的性能。

设计可以故障转移的解决方案时，请注意以下几点：

- 在每个区域预先创建所有必需的服务，例如 Batch 帐户和存储帐户。 创建帐户通常不收取任何费用，只有使用帐户或存储数据时才产生费用。
- 确保提前在所有订阅上设置适当[配额](batch-quota-limit.md)，这样就能够使用 Batch 帐户分配必需的核心数。
- 使用模板和/或脚本在区域中自动部署应用程序。
- 始终更新所有区域中的应用程序二进制文件和引用数据。 保持更新可确保将区域快速联机，不需等待文件的上传和部署。 例如，如果使用 Batch 应用程序包来存储和引用需安装在池节点中的自定义应用程序，则在生成新版应用程序后，应将其上传到每个 Batch 帐户并通过池配置对其进行引用（或者使新版本成为默认版本）。
- 在调用 Batch、存储和任何其他服务的应用程序中，可以轻松地将客户端或负载切换到不同的区域。
- 在正常操作过程中，请考虑频繁切换到备用区域。 例如，如果两个部署位于不同的区域，则每月切换到备用区域一次。

## <a name="next-steps"></a>后续步骤

- 详细了解如何通过 [Azure 门户](batch-account-create-portal.md)、[Azure CLI](./scripts/batch-cli-sample-create-account.md)、[PowerShell](batch-powershell-cmdlets-get-started.md) 或 [Batch 管理 API](batch-management-dotnet.md) 创建 Batch 帐户。
- 了解[与 Batch 帐户关联的默认配额](batch-quota-limit.md)，以及如何增加配额。
