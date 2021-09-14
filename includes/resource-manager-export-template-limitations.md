---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/31/2021
ms.author: tomfitz
ms.openlocfilehash: 76c1ed0a2a1b1a2cca77988d218e2460f99003e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436191"
---
## <a name="limitations"></a>限制

从资源组或资源进行导出时，将通过每种资源类型的[已发布架构](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas)生成导出的模板。 有时，架构没有资源类型的最新版本。 检查导出的模板，确保其包含所需的属性。 如有必要，请编辑导出的模板，以使用所需的 API 版本。

导出模板功能不支持导出 Azure 数据工厂资源。 若要了解如何导出数据工厂资源，请参阅[在 Azure 数据工厂中复制或克隆数据工厂](../articles/data-factory/copy-clone-data-factory.md)。

若要导出通过经典部署模型创建的资源，必须[将其迁移到资源管理器部署模型](../articles/virtual-machines/migration-classic-resource-manager-overview.md)。

如果在导出模板时收到警告，指示未导出某个资源类型，则仍然可以发现该资源的属性。 有关资源属性，请查看[模板参考](/azure/templates)。 还可以查看该资源类型的 [Azure REST API](/rest/api/azure/)。

为其创建导出模板的资源组中存在 200 个资源的限制。 如果尝试导出超过 200 个资源的资源组，则会显示“`Export template is not supported for resource groups more than 200 resources`”错误消息。
