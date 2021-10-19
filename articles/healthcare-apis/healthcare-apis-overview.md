---
title: 什么是 Azure Healthcare APIs？
description: 本文是 Azure 医疗保健 Api 的概述。
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: 01d808813d944d243582cc631f960a241061b46f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782041"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>什么是 Azure 医疗保健 Api (预览) ？

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

你使用的运行状况数据将分散到多个系统和格式。 管理此数据很难，尝试从中获取见解是不可能的。 你需要寻找一种方法来将所有这些不同的系统和数据组合在一起。 使用一种统一的运行状况数据方法，可以发现操作和临床见解，连接新的最终用户应用程序，或启用新的研究项目。 Azure 医疗保健 Api 是一组工具和连接器，使你能够通过将不同的 PHI 组合在一起，并将其与用于机器学习、分析和 AI 的工具以端到端的方式连接，来改善医疗保健。

Azure 医疗保健 Api 具有以下优势：
* 通过以一致的方式在一个位置集中收集和访问数据，使新工作负荷能够利用 PHI。
* 通过将不同的 PHI 结合在一起，并将其与用于机器学习、分析和 AI 的工具进行端到端连接，发现新的见解。
* 在受信任的云上进行构建，并自信地管理、存储和提供受保护的健康信息。
除了 FHIR 以外，新 Microsoft Azure 医疗保健 api 还支持其他医疗保健行业数据标准，如 DICOM，扩展医疗保健数据互操作性。 业务模型和基础结构平台经过了重新设计，以适应不同和未来的医疗保健数据标准的扩展和引入。 在相同的符合性规定下，客户可以使用不同类型的运行状况数据。 内置于托管服务中的工具使客户能够将数据从旧的或设备专有格式转换为 FHIR。 这些工具中的一些已开发和开放：其他将是新的网络。

使用 Azure Healthcare APIs 可以： 
* 快速连接各种不同的健康数据源和格式（例如结构化、影像和设备数据），并将其规范化以便在云中持久保存。
* 转换数据并将其引入 FHIR。 例如，可将传统格式（例如 HL7v2 或 CDA）的健康数据或者设备专有格式的高频率 IoT 数据转换为 FHIR 数据。
* 将 Healthcare APIs 中存储的数据连接到整个 Azure 生态系统中的服务（例如 Synapse）和整个 Microsoft 中的产品（例如 Teams），以通过分析和机器学习派生新的见解，并实现新的工作流以及与 FHIR 应用程序中的 SMART 的连接。
* 通过可提供可靠性、可伸缩性和安全性的企业功能来管理高级工作负载，确保数据受到保护，并满足医疗保健行业的隐私性与合规性认证要求。


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Azure 医疗保健 Api 与用于 FHIR 的 Azure API 之间的主要区别是什么？

**链接服务**

Azure 医疗保健 Api 现在支持多种运行状况数据标准来交换结构化数据。 使用单个 Azure 医疗保健 api 集合，可将不同服务类型的多个实例部署 (FHIR service、DICOM 服务和 IoT Connector) ，从而无缝地与其他服务类型进行协作。

**DICOM 服务简介**

Azure 医疗保健 Api 现在包含对 DICOM 服务的支持。 DICOM 启用了图像数据及其关联元数据的安全交换。 DICOM 是一种国际标准，用于传输、存储、检索、打印、处理和显示医疗图像信息，并且是医疗保健行业接受的主要医学成像标准。 有关 DICOM 服务的详细信息，请参阅 [Dicom 概述](./dicom/dicom-services-overview.md)。

**FHIR 服务的增量更改**

对于 FHIR 数据的安全交换，医疗保健 Api 提供了用于 FHIR 的 Azure API 中可用的一些增量功能。 
* 事务支持：在医疗保健 Api 中，FHIR 服务支持事务捆绑。 有关事务捆绑的详细信息，请访问 HL7.org 并参阅批处理/事务交互。
* 链式搜索改进：链式搜索 & 保留链式搜索不再受100项/子查询的限制。


## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 医疗保健 Api，请遵循5分钟的快速入门来部署工作区。

> [!div class="nextstepaction"]
> [在 Azure 门户中部署工作区](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [工作区概述](workspace-overview.md)
