---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 05/25/2021
ms.openlocfilehash: 205ce8598a363f55f5e87a93ff0074cd189750f6
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113735254"
---
| 资源类型 | 好处 | 资源共享和使用 | [定价和计费模型](../articles/logic-apps/logic-apps-pricing.md) | [限制管理](../articles/logic-apps/logic-apps-limits-and-config.md) |
|---------------|----------|----------------------------|---------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **逻辑应用（消耗）** <p><p>主机环境：多租户 | - 最容易入门 <p><p>- 仅为你使用的部分付费 <p><p>- 完全托管 | 单个逻辑应用只单个能有一个工作流。 <p><p>跨多个租户的客户创建的逻辑应用共享相同的处理（计算）、存储和网络等。 | [消耗](../articles/logic-apps/logic-apps-pricing.md#consumption-pricing)（即用即付） | Azure 逻辑应用管理这些限制的默认值，但如果特定限制存在更改值的选项，则可以更改其中某些值。 |
| **逻辑应用（标准版）** <p><p>主机环境： <br>单租户 | - 使用容器化单租户 Azure 逻辑应用运行时，在 Azure Functions 可以运行的任何位置运行。 当前不支持部署槽位。 <p><p>- 更多的内置连接器，可大规模实现更高吞吐量和更低成本 <p><p>- 围绕运行时和性能设置的更多控制和微调功能 <p><p>- 集成了对虚拟网络和专用终结点的支持。 <p><p>- 创建自己的内置连接器。 | 单个逻辑应用可以有多个有状态和无状态[ ](../articles/logic-apps/single-tenant-overview-compare.md#stateful-stateless)工作流。 <p><p>单个逻辑应用和租户中的工作流共享相同的处理（计算）、存储和网络等。 | [标准](../articles/logic-apps/logic-apps-pricing.md#standard-pricing)，基于处于所选定价层的托管计划 <p><p>如果运行有状态工作流（这些工作流使用[外部存储](../articles/azure-functions/storage-considerations.md#storage-account-requirements)，Azure 逻辑应用运行时将执行遵循 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)的存储事务。 | 你可以根据方案的需求更改多个限制的默认值。 <p><p>重要说明：某些限制的具有固定的上限。 在 Visual Studio Code 中，你对逻辑应用项目配置文件中的默认限制值的更改不会反应在设计器体验中。 有关详细信息，请参阅[在单租户 Azure 逻辑应用中编辑中逻辑应用的应用和环境设置](../articles/logic-apps/edit-app-settings-host-settings.md)。 |
| **逻辑应用（消耗）** <p><p>主机环境： <br>集成服务环境 (ISE) | - 适用于大型工作负载的企业规模 <p><p>- 20 多个特定于 ISE 的连接器，可直接连接到虚拟网络 <p><p>- 包含使用情况和客户控制缩放的可预测定价 | 单个逻辑应用只单个能有一个工作流。 <p><p>同一环境中的逻辑应用共享相同的处理（计算）、存储和网络等。 | [ISE](../articles/logic-apps/logic-apps-pricing.md#ise-pricing)（固定） | Azure 逻辑应用管理这些限制的默认值，但如果特定限制存在更改值的选项，则可以更改其中某些值。 |
||||||