---
title: 教程 - Da Vinci 药物公式 - Azure API for FHIR
description: 本教程逐步介绍如何设置Azure API for FHIR测试，以通过针对 DaVinci 药物公式实现指南的 Touchstone 测试。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 06/07/2021
ms.openlocfilehash: fc66de13c02d6d04e8a156937de70775b9053da3
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751084"
---
# <a name="da-vinci-drug-formulary"></a>Da Vinci 药物公式

在本教程中，我们将演练如何设置 Azure API for FHIR，以通过[Da Vinci Payer Data Exchange US Formulary](http://hl7.org/fhir/us/Davinci-drug-formulary/)实现指南 的[Touchstone](https://touchstone.aegis.net/touchstone/)测试。

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一个测试是针对 [Da Vinci](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)Azure API for FHIR公式功能语句 测试该测试。 如果在没有任何更新的情况下运行此测试，则测试将因缺少搜索参数和缺少配置文件而失败。

### <a name="define-search-parameters"></a>定义搜索参数

作为 Da Vinci 药物公式 IG 的一部分，需要为[](how-to-do-custom-search.md)FormularyDrug 资源定义三个新的搜索参数。 这三项均在功能语句中进行测试。

* [可进行](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [PlanPlan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [一元名称](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Da Vinci 药物公式 IG 所需的其余搜索参数由基本规范定义，已在 Azure API for FHIR中提供，无需任何更新。

### <a name="store-profiles"></a>存储配置文件

除了定义搜索参数外，还需要进行其他更新才能通过此测试，即加载 [所需的配置文件](validation-against-profiles.md)。 有两个配置文件用作 Da Vinci 药物公式 IG 的一部分。

* [公式药物](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [公式覆盖率计划](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>示例 rest 文件

为帮助创建这些搜索参数和配置文件，我们在开放源代码站点上有 [Da Vinci Formulary](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) 示例 HTTP 文件，该文件包含上述所有步骤（在单个文件中）。 上传所有必要的配置文件和搜索参数后，可以在 Touchstone 中运行功能语句测试。 应会成功运行：

:::image type="content" source="media/cms-tutorials/davinci-test-script-execution.png" alt-text="Da Vinci 测试脚本执行。":::

## <a name="touchstone-query-test"></a>Touchstone 查询测试

第二个测试是 [查询功能](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)。 此测试验证你能否使用各种参数搜索特定的覆盖率计划和药物资源。 最佳路径是针对数据库中已有的资源进行测试，但我们还有 [Da VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) HTTP 文件，可从 IG 中的示例拉取的示例资源，可用于创建资源并测试这些资源。

:::image type="content" source="media/cms-tutorials/davinci-test-execution-results.png" alt-text="Da Vinci 测试执行结果。":::

## <a name="next-steps"></a>后续步骤

在本教程中，我们演练了如何在 Touchstone 中传递 Da Vinci Payer Data Exchange US Formulary。 接下来，可以了解如何在 Touchstone 中测试 Da Vinci PDex 实现指南。

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)