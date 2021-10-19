---
title: CARIN Blue Button Implementation Guide for Blue Button
description: 本教程逐步介绍了如何设置 FHIR 服务，以通过适用于 C4BB IG 的 CARIN 实现指南的 Touchstone (C4BB IG) 。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/03/2021
ms.openlocfilehash: 4aebd92ea55e789a42444bc488dcaf2c02b2acb1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778357"
---
# <a name="carin-implementation-guide-for-blue-button174"></a>Blue Button&#174; 的 CARIN 实现指南

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本教程中，我们将演练在 Azure 医疗保健 API (中设置 FHIR 服务（称为 FHIR 服务) ）以通过 Blue Button (C4BB IG) 的[CARIN](https://build.fhir.org/ig/HL7/carin-bb/index.html)实现指南的[Touchstone](https://touchstone.aegis.net/touchstone/)测试。

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一个测试是针对 [C4BB IG](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)功能语句 测试 FHIR 服务。 如果在没有任何更新的情况下针对 FHIR 服务运行此测试，则测试将因缺少搜索参数和缺少配置文件而失败。 

### <a name="define-search-parameters"></a>定义搜索参数

作为 C4BB IG 的一部分，需要为资源定义三 [个新的](how-to-do-custom-search.md) 搜索 `ExplanationOfBenefit` 参数。 其中两个在功能语句中测试了 (类型和服务日期) ，搜索和 (搜索 `_include`) 。  

* type
* [service-date](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [保险人](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> 在这些搜索参数的原始 JSON 中，名称设置为 `ExplanationOfBenefit_<SearchParameter Name>` 。 Touchstone 测试预期这些 的名称将为 **类型**、**服务日期** 和 **。**  
 
C4BB IG 所需的其余搜索参数由基本规范定义，已在 FHIR 服务中提供，无需任何其他更新。
 
### <a name="store-profiles"></a>存储配置文件

除了定义搜索参数外，还需要进行另一项更新才能通过此测试，即加载 [所需的配置文件](validation-against-profiles.md)。 C4BB IG 中定义了八个配置文件。 

* [C4BB 覆盖范围](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 
* [C4BB 说明OfBenefit Inpatient 机构](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 
* [C4BB 说明OfBenefit 国家/直方机构](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 
* [C4BB 说明OfBenefit 中](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 
* [C4BB 说明OfBenefit Professional NonClinician](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 
* [C4BB 组织](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 
* [C4BB 患者](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 
* [C4BB 实践者](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>示例 rest 文件

为了协助创建这些搜索参数和配置文件，我们有一个示例 [http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) 文件，其中包含上述所有步骤，在单个文件中。 上传所有必要的配置文件和搜索参数后，可以在 Touchstone 中运行功能语句测试。

:::image type="content" source="media/centers-medicare-services-tutorials/capability-test-script-execution-results.png" alt-text="功能测试脚本执行结果。":::

## <a name="touchstone-read-test"></a>触控板读取测试

测试功能语句后，我们将针对 C4BB [](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) IG 测试 FHIR 服务的读取功能。 此测试针对第一个测试中加载的八个配置文件测试一致性。 需要加载符合配置文件的资源。 最佳路径是针对数据库中已有的资源进行测试，但我们还有一个 [http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) 文件，其中提供了从 IG 中的示例提取的示例资源，可用于创建资源并测试这些资源。

:::image type="content" source="media/centers-medicare-services-tutorials/test-execution-results-touchstone.png" alt-text="触控删除读取测试执行结果。":::

## <a name="touchstone-eob-query-test"></a>Touchstone EOB 查询测试

我们将查看的下一个测试是 [EOB 查询测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS)。 如果已完成读取测试，则加载了所需的所有数据。 此测试验证是否可以使用各种参数 `Patient` `ExplanationOfBenefit` 搜索特定的 和资源。

:::image type="content" source="media/centers-medicare-services-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Touchstone EOB 查询执行结果。":::

## <a name="touchstone-error-handling-test"></a>触控删除错误处理测试

我们将演练的最后一个测试是测试 [错误处理](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS)。 唯一需要执行的步骤是从数据库中删除 ExplanationOfBenefit 资源，并使用测试中已删除资源的 `ExplanationOfBenefit` ID。

:::image type="content" source="media/centers-medicare-services-tutorials/test-execution-touchstone-error-handling.png" alt-text="触控删除 EOB 错误处理结果。":::


## <a name="next-steps"></a>后续步骤

在本教程中，我们演练了如何在 Touchstone 中通过用于蓝色按钮的 CARIN IG 测试。 接下来，你可以查看如何测试 Da Vinci 公式测试。

>[!div class="nextstepaction"]
>[DaVinci 药物公式](davinci-drug-formulary-tutorial.md)       
 
