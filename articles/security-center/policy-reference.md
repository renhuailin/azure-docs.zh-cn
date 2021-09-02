---
title: Azure 安全中心的内置策略定义
description: 列出 Azure 安全中心的 Azure Policy 内置策略定义。 这些内置的策略定义提供了管理 Azure 资源的常用方法。
ms.date: 08/27/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: c8928a3a4d19aa7fecb751eb0a40a23c9f72f592
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100258"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Azure 安全中心的 Azure Policy 内置定义

此页是与 Azure 安全中心相关的 [Azure Policy](../governance/policy/overview.md) 内置策略定义的索引。 策略定义进行下列分组：

- [计划](#azure-security-center-initiatives)组在“安全中心”类别中列出 Azure Policy 计划定义。
- [默认计划](#azure-security-center-initiatives)组列出了属于安全中心默认计划（即 [Azure 安全基准](/security/benchmark/azure/introduction)）的所有 Azure Policy 定义。 此 Microsoft 编写的基准受到广泛认可，以 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制措施为基础构建，重点关注以云为中心的安全性。
- [类别](#azure-security-center-category)组列出“安全中心”类别中的所有 Azure Policy 定义。

有关安全策略的详细信息，请参阅[使用安全策略](./tutorial-security-policy.md)。 有关其他服务的其他 Azure Policy 内置定义，请参阅 [Azure Policy 内置定义](../governance/policy/samples/built-in-policies.md)。

每个内置策略定义链接（指向 Azure 门户中的策略定义）的名称。 使用“版本”列中的链接查看 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy)上的源。

## <a name="azure-security-center-initiatives"></a>Azure 安全中心计划

若要了解通过安全中心监视的内置计划，请参阅下表：

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>安全中心的默认计划（Azure 安全基准）

若要了解通过安全中心监视的内置策略，请参阅下表：

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure 安全中心类别

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的 Azure Policy 安全策略定义。 若要了解有关计划、策略及其与安全中心建议的关系的详细信息，请参阅[什么是安全策略、计划和建议？](security-policy-concept.md)。
