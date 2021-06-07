---
title: CARIN 的蓝色按钮&#174; 实现指南-用于 FHIR 的 Azure API
description: 本教程将指导你完成以下步骤：设置适用于 FHIR 的 Azure API，以通过 Touchstone 测试为 CARIN 实现指南 (C4BB IG) 。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 05/27/2021
ms.openlocfilehash: 25be80678389b59e907caf60529a8e4048a959e0
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562642"
---
# <a name="carin-implementation-guide-for-blue-button174"></a>蓝色按钮&#174; 的 CARIN 实现指南

在本教程中，我们将演练如何设置用于 FHIR 的 Azure API，以通过蓝色按钮 (C4BB IG) 来传递[CARIN 实现指南](https://build.fhir.org/ig/HL7/carin-bb/index.html)的[Touchstone](https://touchstone.aegis.net/touchstone/)测试。

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一次测试是针对 [C4BB IG 功能声明](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)测试适用于 FHIR 的 Azure API。 如果对 FHIR 的 Azure API 运行此测试，而不进行任何更新，则测试将失败，因为缺少搜索参数，缺少配置文件。 


### <a name="define-search-parameters"></a>定义搜索参数

作为 C4BB IG 的一部分，需要为资源定义三个 [新的搜索参数](how-to-do-custom-search.md) `ExplanationOfBenefit` 。 其中两个在功能声明 (类型和服务-日期) 中测试， `_include` (保险公司) 搜索需要一个。  

* type
* [服务-日期](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [保险公司](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> 在这些搜索参数的原始 JSON 中，名称设置为 `ExplanationOfBenefit_<SearchParameter Name>` 。 Touchstone 测试的名称应为 **类型**、 **服务日期** 和 **保险公司**。  
 
C4BB IG 所需的搜索参数的其余部分由基本规范定义，并已在 Azure API for FHIR 中提供，无需任何其他更新。
 
### <a name="store-profiles"></a>存储配置文件

在定义搜索参数外，要通过此测试所需的另一个更新是加载 [所需的配置文件](validation-against-profiles.md)。 在 C4BB IG 中定义了八个配置文件。 

* [C4BB 覆盖率](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 

* [Inpatient C4BB ExplanationOfBenefit](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 

* [门诊 C4BB ExplanationOfBenefit](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 

* [C4BB ExplanationOfBenefit 药房](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 

* [C4BB ExplanationOfBenefit Professional NonClinician](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 

* [C4BB 组织](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 

* [C4BB 患者](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 

* [C4BB 实践者](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>示例 rest 文件

为了帮助创建这些搜索参数和配置文件，我们提供了一个 [示例 http 文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) ，其中包含上述所有步骤（单个文件）。 上载所有必要的配置文件和搜索参数后，可以在 Touchstone 中运行功能声明测试。

:::image type="content" source="media/cms-tutorials/capability-test-script-execution-results.png" alt-text="功能测试脚本执行结果。":::

## <a name="touchstone-read-test"></a>Touchstone 读取测试

测试功能声明后，我们将针对 C4BB IG 测试 Azure API for FHIR 的 [读取功能](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) 。 此测试针对第一次测试中加载的八个配置文件进行测试。 你将需要加载符合配置文件的资源。 最好的路径是对数据库中已有的资源进行测试，但我们还提供了一个 [http 文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) ，其中包含从 IG 中提取的示例资源，可用于创建资源和测试。

:::image type="content" source="media/cms-tutorials/test-execution-results-touchstone.png" alt-text="Touchstone 读取测试执行结果。":::

## <a name="touchstone-eob-query-test"></a>Touchstone EOB 查询测试

下一次测试是 [EOB 查询测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS)。 如果已完成读取测试，则会加载所需的所有数据。 此测试验证是否可以使用各种参数搜索特定患者和权益资源的说明。

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Touchstone EOB 查询执行结果。":::

## <a name="touchstone-error-handling-test"></a>Touchstone 错误处理测试

我们将逐步完成的最后一次测试是测试 [错误处理](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS)。 你需要做的唯一一步是从数据库中删除 ExplanationOfBenefit 资源，并使用测试中 delete ExplanationOfBenfit 资源的 ID。

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-error-handling.png" alt-text="Touchstone EOB 错误处理结果。":::


## <a name="next-steps"></a>后续步骤

在本教程中，我们演练了如何在 Touchstone 中传递 CARIN IG 以进行蓝色按钮测试。 接下来，可以查看如何测试 Da Vinci 处方测试。

>[!div class="nextstepaction"]
>[Davinci.txt 药品处方](davinci-drug-formulary-tutorial.md)       
 