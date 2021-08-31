---
title: 从 Azure 德国迁移到全球 Azure
description: 介绍如何将 Azure 资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 37dde9cf2f105c3e8e8c3dc86e59340c205f9254
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "117029132"
---
# <a name="overview-of-migration-guidance-for-azure-germany"></a>Azure 德国迁移指南概述

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本节文章创建的目的是帮助将工作负荷从 Azure 德国迁移到全球 Azure。 尽管 [Azure 迁移中心](https://azure.microsoft.com/migration/)提供了帮助迁移资源的工具，但 Azure 迁移中心中的某些工具仅适用于同一租户或同一区域中发生的迁移。

德国的两个区域完全独立于全球 Azure。 全球 Azure 的云和德国的云具有自己独立的 Azure Active Directory (Azure AD) 实例。 因此，Azure 德国租户独立于全球 Azure 租户。 本文介绍在 *不同* 租户之间迁移时可用的迁移工具。

有关标识/租户的指南适用于仅限 Azure 的客户。 如果使用适用于 Azure 和 Microsoft 365（或其他 Microsoft 产品）的通用 Azure Active Directory (Azure AD) 租户，则标识迁移会有复杂性，应该首先阅读[从 Microsoft 云 Deutschland 迁移的迁移阶段操作和影响](/microsoft-365/enterprise/ms-cloud-germany-transition-phases?view=o365-worldwide)。 如果有疑问，请与帐户管理员或 Microsoft 支持部门联系。

Azure 云解决方案提供商需要采取其他步骤，以在转换到新的德国数据中心区域期间和之后支持客户。 详细了解[其他步骤](/microsoft-365/enterprise/ms-cloud-germany-transition-add-csp)。

## <a name="migration-process"></a>迁移过程

用于将工作负荷从 Azure 德国迁移到全球 Azure 的过程通常与用于将应用程序迁移到云的过程相似。 迁移过程的步骤如下：

![此图显示了迁移过程的四个步骤：评估、计划、迁移、验证](./media/germany-migration-main/migration-steps.png)

### <a name="assess"></a>评估

通过将 Azure 帐户所有者、订阅管理员、租户管理员和财务和会计团队集合在一起，了解组织的 Azure 德国足迹非常重要。 这些角色的工作人员可以为大型组织提供 Azure 使用情况的完整画面。

在评估阶段，编译资源的清单：
  - 每个订阅管理员和租户管理员都应该运行一系列脚本来列出资源组、每个资源组中的资源以及资源组部署设置。
  - 记录跨 Azure 中应用程序和与外部系统的依赖项。
  - 记录每个 Azure 资源的计数以及与要迁移的每个实例相关联的数据量。
  - 确保应用程序体系结构文档与 Azure 资源列表一致。

在此阶段结束时，会具有：

- 正在使用的 Azure 资源的完整列表。
- 资源之间的依赖项列表。
- 有关迁移工作量的复杂性的信息。

### <a name="plan"></a>计划

在规划阶段，完成以下任务：

- 使用在评估阶段中完成的依赖项分析的输出来定义相关组件。 考虑在“迁移包”中一起迁移相关组件。
- （可选）借迁移的机会来应用 [Gartner 5-R 标准](https://www.gartner.com/en/documents/3873016/evaluation-criteria-for-cloud-management-platforms-and-t)和优化工作负荷。
- 确定全球 Azure 中的目标环境：
  1. 确定目标全球 Azure 租户（如有必要，创建一个）。
  1. 创建订阅。
  1. 选择要迁移的全球 Azure 位置。
  1. 执行匹配 Azure 德国中的体系结构和全球 Azure 中的体系结构的测试迁移方案。
- 确定适合迁移的时间线和计划。 为每个迁移包创建用户验收测试计划。

### <a name="migrate"></a>Migrate

在迁移阶段，使用 Azure 德国迁移文章中讨论的工具、技术和建议在全球 Azure 中创建新的资源。 然后，配置应用程序。

### <a name="validate"></a>验证

在验证阶段，完成以下任务：

1. 执行用户验收测试。
1. 确保应用程序按预期方式工作。
1. 将最新的数据同步到目标环境（如果适用）。
1. 切换到全球 Azure 中的新应用程序实例。
1. 验证生产环境是否按预期方式工作。
1. 停用 Azure 德国中的资源。

## <a name="terms"></a>术语

Azure 德国迁移文章中使用了以下术语：

“源”描述从哪里迁移资源（例如，Azure 德国）：

- “源租户名称”：Azure 德国中租户的名称（账户名中“\@”后面的所有内容）。  Azure 德国中的租户名称全部以“microsoftazure.de”结束。
- “源租户 ID”：Azure 德国中的租户的 ID。 在 Azure 门户中，将鼠标移到右上角的账户名上时，租户 ID 出现。
- “源订阅 ID”：Azure 德国中的资源订阅的 ID。 同一租户中可以有多个订阅。 始终确保使用正确的订阅。
- “源区域”：德国中部（“germanycentral”）或德国东北部（“germanynortheast”），具体取决于要迁移的资源所在的位置。  

“目标”描述将资源迁移到哪里： 

- “目标租户名称”：全球 Azure 中的租户的名称。
- “目标租户 ID”：全球 Azure 中的租户的 ID。
- “目标订阅 ID”：全球 Azure 中的资源的订阅 ID。
- “目标区域”：可以使用全球 Azure 中的几乎任何区域。 很可能会想要将资源迁移到西欧（“westeurope”）或北欧（“northeurope”）。 

> [!NOTE]
> 验证要迁移的 Azure 服务是否在目标区域中提供。 Azure 德国中可用的所有 Azure 服务在西欧中都可用。 Azure 德国中可用的所有 Azure 服务在北欧中都可用，Azure 机器学习工作室（经典版）和 Azure 虚拟机中的 G/GS VM 系列除外。

最好在浏览器中为源和目标门户添加书签：

- Azure 德国门户位于 [https://portal.microsoftazure.de/](https://portal.microsoftazure.de/)。
- 全球 Azure 门户位于 [https://portal.azure.com/](https://portal.azure.com/)。

## <a name="next-steps"></a>后续步骤

了解用于迁移以下服务类别中的资源的工具、技术和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)
