---
title: 教程-Da Vinci 计划 Net-用于 FHIR 的 Azure API
description: 本教程介绍如何设置用于 FHIR 的 Azure API，以便为 Da Vinci 付款人数据传递 Touchstone 测试 Exchange 实施指南。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 06/25/2021
ms.openlocfilehash: bb7b7e3813a317aa1b1a9f97ab4f80650eb2fd1e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285902"
---
# <a name="da-vinci-plan-net"></a>Da Vinci Plan Net

在本教程中，我们将逐步介绍如何设置 Azure API for FHIR，以便通过 [Touchstone](https://touchstone.aegis.net/touchstone/) 测试为 DA Vinci PDEX 付款人网络 (Plan-Net) 实施指南。

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一次测试是针对 [Da Vinci Plan-Net 功能声明](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)测试用于 FHIR 的 Azure API。 如果在未进行任何更新的情况下运行此测试，则测试将失败，因为缺少搜索参数和缺少配置文件。

## <a name="define-search-parameters"></a>定义搜索参数

作为 Da Vinci Plan-Net IG 的一部分，你需要为医疗保健服务、保险计划、实践者角色、组织和组织隶属关系资源定义六个 [新的搜索参数](./how-to-do-custom-search.md) 。 这六项功能都在功能语句中进行了测试：

* [医疗保健服务覆盖区](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [保险计划覆盖区](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [保险计划计划类型](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [组织覆盖区](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [组织隶属关系网络](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [实践者角色网络](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> 在这些搜索参数的原始 JSON 中，名称设置为 `Plannet_sp_<Resource Name>_<SearchParameter Name>` 。 Touchstone 测试要求它们的名称将只是 `SearchParameter Name` (覆盖率-区域、计划类型或网络) 。

Da Vinci Plan-Net IG 所需的搜索参数的其余部分由基本规范定义，并已在适用于 FHIR 的 Azure API 中提供，无需任何其他更新。

## <a name="store-profiles"></a>存储配置文件

在定义搜索参数外，你需要加载 [所需的配置文件和扩展](./validation-against-profiles.md#storing-profiles) 以通过此测试。 有九个配置文件用作 Da Vinci Plan-Net IG 的一部分：

* [计划-网络终结点](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [计划-网络医疗保健服务](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [计划-网络 InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [计划-网络位置](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [规划-网络网络](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [规划-网络组织](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [计划-网络 OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [计划-网络实践者](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [计划-网络 PractitionerRole](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>示例 rest 文件

为了帮助创建这些搜索参数和配置文件，我们在开源站点上有一个示例 http 文件，其中包括上述所有步骤。 上载所有必要的配置文件和搜索参数后，可以在 Touchstone 中运行功能声明测试。

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Da Vinci 计划网络示例 rest 测试执行脚本已通过":::

## <a name="touchstone-error-handling-test"></a>Touchstone 错误处理测试

我们将演练的第二个测试是测试 [错误处理](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS)。 唯一必须执行的步骤是从数据库中删除 HealthcareService 资源，并在测试中使用已删除的 HealthcareService 资源的 ID。 HealthcareService 站点中的示例 [DaVinci_PlanNet http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) 文件提供了一个示例，用于在此步骤中发布和删除。

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da Vinci 计划 net touchstone 错误测试执行脚本已通过":::

## <a name="touchstone-query-test"></a>Touchstone 查询测试

下一次测试是 [查询功能测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)。 此测试针对在第一次测试中加载的配置文件进行测试。 需要加载符合配置文件的资源。 最好的路径是对数据库中已有的资源进行测试，但是，还会有 [DaVinci_PlanNet_Sample_Resources 的 http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) 文件，其中包含从 IG 中提取的示例资源，可用于创建资源并对其进行测试。  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Da Vinci 计划网络查询测试失败":::

> [!NOTE]
> 提供示例资源后，应预计查询测试的成功率为98%：

> * 针对 FHIR 服务器提出了开放式 GitHub 问题，导致其中一项测试失败：[如果资源同时满足基本标准和 _include 标准，则返回多次问题 #2037 ·microsoft/fhir (github.com) ](https://github.com/microsoft/fhir-server/issues/2037)

## <a name="next-steps"></a>后续步骤

在本教程中，我们逐步完成了设置 Azure API for FHIR，以便通过 Da Vinci PDEX 付款人网络的 Touchstone 测试 (Plan-Net) 实施指南。 接下来，可以了解适用于 FHIR 的所有 Azure API 功能。

>[!div class="nextstepaction"]
>[受支持的功能](fhir-features-supported.md)