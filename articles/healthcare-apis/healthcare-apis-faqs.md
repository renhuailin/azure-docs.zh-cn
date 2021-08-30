---
title: 有关 Azure Healthcare APIs 的常见问题解答
description: 本文档解答有关 Azure Healthcare APIs 的常见问题。
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 07/16/2021
ms.author: ginle
ms.openlocfilehash: 3cccd2a1e418dbc66a5a8c1bfa665c6c3276552d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778653"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>有关 Azure Healthcare APIs（预览版）的常见问题解答

下面是有关 Azure Healthcare APIs 的常见问题。

## <a name="azure-healthcare-apis-the-basics"></a>Azure Healthcare APIs：基础知识

### <a name="what-is-the-azure-healthcare-apis"></a>什么是 Azure Healthcare APIs？
Azure Healthcare APIs 是完全托管的健康数据平台，使用它可以通过快速医疗保健互操作性资源 (FHIR®) 及医学数字成像和通信 (DICOM®) 等可互操作的开放行业标准，快速交换和持久保存受保护健康信息 (PHI) 与健康数据。

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>Azure Healthcare APIs 有哪些作用？
使用 Azure Healthcare APIs 可以： 

* 快速连接各种不同的健康数据源和格式（例如结构化、影像和设备数据），并将其规范化以便在云中持久保存。

* 转换数据并将其引入 FHIR。 例如，可将传统格式（例如 HL7v2 或 CDA）的健康数据或者设备专有格式的高频率 IoT 数据转换为 FHIR 数据。

* 将 Healthcare APIs 中存储的数据连接到整个 Azure 生态系统中的服务（例如 Synapse）和整个 Microsoft 中的产品（例如 Teams），以通过分析和机器学习派生新的见解，并实现新的工作流以及与 FHIR 应用程序中的 SMART 的连接。

* 通过可提供可靠性、可伸缩性和安全性的企业功能来管理高级工作负载，确保数据受到保护，并满足医疗保健行业的隐私性与合规性认证要求。

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>是否可将现有的生产工作负载从 Azure API for FHIR 迁移到 Healthcare APIs？
很遗憾，我们目前不提供迁移功能。 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>Azure Healthcare APIs 的定价如何？
公共预览版的 Azure Healthcare APIs 可供免费使用

### <a name="what-regions-are-healthcare-apis-available"></a>哪些区域推出了 Healthcare APIs？
有关最新信息，请参阅[各区域的产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir)页。 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>Azure Healthcare APIs 的订阅配额限制是多少？

#### <a name="workspace-logical-container"></a>工作区（逻辑容器）：
* 每个订阅 200 个实例（不可调整）

#### <a name="dicom-server"></a>DICOM 服务器：
* 每个订阅 800 个实例（不可调整）
* 每个工作区 10 个 DICOM 实例（不可调整）

#### <a name="fhir-server"></a>FHIR 服务器：
* 每个订阅 25 个实例（不可调整）
* 每个工作区 10 个 FHIR 实例（不可调整）

#### <a name="iot-connector"></a>IoT 连接器：
* 每个订阅 25 个 IoT 连接器（不可调整）
* 每个工作区 10 个 IoT 连接器（不可调整）
* 每个 IoT 连接器 1 个 FHIR 目标*（不可调整）。

## <a name="more-frequently-asked-questions"></a>更多常见问题
[有关 Azure Healthcare APIs FHIR 服务的常见问题解答](./fhir/fhir-faq.md)

[有关 Azure Healthcare APIs DICOM 服务的常见问题解答](./dicom/dicom-services-faqs.yml)


