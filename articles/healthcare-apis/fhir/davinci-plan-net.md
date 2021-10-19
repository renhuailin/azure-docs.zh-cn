---
title: 教程 - Da Vinci Plan Net - Azure API for FHIR
description: 本教程逐步介绍如何设置Azure API for FHIR Da Vinci Payer Data Exchange实现指南的 Touchstone 测试。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/03/2021
ms.openlocfilehash: 9f45db7642128d504d908b7ca26716d2940a7f3c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778024"
---
# <a name="da-vinci-plan-net"></a>Da Vinci Plan Net

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本教程中，我们将演练在 Azure 医疗保健 API (（称为 FHIR 服务) ）中设置 FHIR 服务，以通过 Da Vinci PDEX Payer Network (Plan-Net) 实现指南的 [Touchstone](https://touchstone.aegis.net/touchstone/) 测试。

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一个测试是针对 [Da Vinci Plan-Net功能语句 测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)FHIR 服务。 如果在没有任何更新的情况下运行此测试，则测试将因缺少搜索参数和缺少配置文件而失败。

## <a name="define-search-parameters"></a>定义搜索参数

作为 Da Vinci Plan-Net IG 的一部分，需要为医疗保健服务[](how-to-do-custom-search.md)、保险计划、实践者角色、组织和组织附属资源定义六个新的搜索参数。 在功能语句中测试所有这些 6 个：

* [医疗保健服务覆盖范围区域](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [保险计划覆盖范围](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [保险计划计划类型](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [组织覆盖范围区域](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [组织附属网络](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [执行者角色网络](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> 在这些搜索参数的原始 JSON 中，名称设置为 `Plannet_sp_<Resource Name>_<SearchParameter Name>` 。 Touchstone 测试预期这些项的名称将仅为 (区域、计划类型或 `SearchParameter Name` 网络) 。

Da Vinci Plan-Net IG 所需的其余搜索参数由基本规范定义，且已在 FHIR 服务中提供，无需任何其他更新。

## <a name="store-profiles"></a>存储配置文件

除了定义搜索参数之外，还需要加载所需的 [配置文件和扩展](./validation-against-profiles.md#storing-profiles) 才能通过此测试。 Da Vinci IG 中使用了 9 个Plan-Net配置文件：

* [Plan-Net 终结点](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-Net Healthcare Service](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [Plan-Net 位置](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-Net 网络](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Plan-Net 组织](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Plan-Net OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Plan-Net 实践者](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-Net 执行者角色](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>示例 rest 文件

为了协助创建这些搜索参数和配置文件，我们在开放源代码站点上有一个示例 http 文件，其中包含上述所有步骤（在单个文件中）。 上传所有必要的配置文件和搜索参数后，可以在 Touchstone 中运行功能语句测试。

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="通过 Da Vinci 计划 net 示例 rest 测试执行脚本":::

## <a name="touchstone-error-handling-test"></a>触控删除错误处理测试

我们将演练的第二个测试是测试 [错误处理](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS)。 唯一必须执行的步骤是从数据库中删除 HealthcareService 资源，并使用测试中已删除的 HealthcareService 资源的 ID。 开源 [站点DaVinci_PlanNet.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) 文件的示例提供了一个示例 HealthcareService，用于发布和删除此步骤。

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da Vinci plan net Touchstone 错误测试执行脚本通过":::

## <a name="touchstone-query-test"></a>Touchstone 查询测试

我们将演练的下一个测试是 [查询功能测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)。 此测试针对第一个测试中加载的配置文件测试一致性。 需要加载符合配置文件的资源。 最佳路径是针对数据库中已有的资源进行测试，但我们还有一个 [DaVinci_PlanNet_Sample_Resources.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) 文件，该文件包含从 IG 中的示例提取的示例资源，可用于创建资源并测试这些资源。  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Da Vinci 计划网络查询测试失败":::

> [!NOTE]
> 提供示例资源后，查询测试的成功率应为 98%：

> * 存在针对 FHIR 服务器的GitHub问题，该问题导致其中一个测试失败：如果资源同时满足基本条件_include[返回了多次 ·问题 #2037 ·microsoft/fhir-server (github.com) ](https://github.com/microsoft/fhir-server/issues/2037)

## <a name="next-steps"></a>后续步骤

在本教程中，我们演练了如何设置 Azure API for FHIR，以通过 Da Vinci PDEX Payer Network (Plan-Net) 实现指南的 Touchstone 测试。 接下来，可以了解所有 FHIR 服务功能。

>[!div class="nextstepaction"]
>[受支持的功能](fhir-features-supported.md)
