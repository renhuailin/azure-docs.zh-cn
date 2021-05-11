---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: c29abc456c5825a2f22b20a4893bb14eb09058af
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107984370"
---
| 资源 | 限制 |
| --- | --- |
| 每个[资源组](../articles/azure-resource-manager/management/overview.md#resource-groups)的资源 | 资源不受资源组限制。 相反，它们受资源组中资源类型的限制。 请参阅下一行。 |
| 每个资源组的资源（按资源类型） |800 - 某些资源类型可能超过 800 的限制。 请参阅[不限于每个资源组 800 个实例的资源](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)。 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |
| 每个部署的资源数 |800 |
| 管理锁数（按唯一的[作用域](../articles/azure-resource-manager/management/overview.md#understand-scope)）  |20 |
| 标记数（按资源或资源组） |50 |
| 标记键长度 |512 |
| 标记值长度 |256 |

<sup>1</sup>当数量接近限额时，将自动从历史记录中删除部署。 从部署历史记录中删除条目不会影响已部署的资源。 有关详细信息，请参阅[从部署历史记录中自动删除](../articles/azure-resource-manager/templates/deployment-history-deletions.md)。

#### <a name="template-limits"></a>模板限制

| Value | 限制 |
| --- | --- |
| parameters |256 |
| 变量 |256 |
| 资源（包括副本计数） |800 |
| Outputs |64 |
| 模板表达式 |24,576 个字符 |
| 已导出模板中的资源 |200 |
| 模板大小 |4 MB |
| 参数文件大小 |4 MB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/templates/linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters)。

如果请求的总大小太大，则可能会收到一个错误消息，指示模板或参数文件应小于 4 MB。 若要详细了解如何简化模板以避免大型请求，请查看[解决超出作业大小的错误](../articles/azure-resource-manager/templates/error-job-size-exceeded.md)。