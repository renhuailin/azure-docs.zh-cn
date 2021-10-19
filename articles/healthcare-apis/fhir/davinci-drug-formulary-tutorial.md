---
title: Da Vinci 药品处方教程
description: 本教程将指导你设置 FHIR 服务，以便针对 Davinci.txt 药品处方实现指南传递 Touchstone 测试。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/06/2021
ms.openlocfilehash: 56cf21f960cadd54340be48deeb44b1e37470f69
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778807"
---
# <a name="tutorial-for-da-vinci-drug-formulary"></a>Da Vinci 药品处方教程

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本教程中，我们将逐步介绍如何在 Azure 医疗保健 api 中设置 FHIR 服务， (特此称为 FHIR service) ，用于传递[](https://touchstone.aegis.net/touchstone/)针对[Da Vinci 付款人数据的 Touchstone 测试 Exchange 美国药品处方实现指南](http://hl7.org/fhir/us/Davinci-drug-formulary/)。

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一次测试是针对 [Da Vinci 药物处方功能声明](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)测试 FHIR 服务。 如果在未进行任何更新的情况下运行此测试，则测试将失败，因为缺少搜索参数和缺少配置文件。

### <a name="define-search-parameters"></a>定义搜索参数

作为 Da Vinci 药品处方 IG 的一部分，需要为 FormularyDrug 资源定义三个 [新的搜索参数](how-to-do-custom-search.md) 。 这三种情况都在功能声明中进行了测试。

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [DrugPlan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [DrugName](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Da Vinci 药品处方 IG 所需的搜索参数的其余部分由基本规范定义，且已在 FHIR 服务中可用，无需再进行任何更新。

### <a name="store-profiles"></a>存储配置文件

在定义搜索参数外，要通过此测试，只需加载 [所需的配置文件](validation-against-profiles.md)即可。 使用两个配置文件作为 Da Vinci 药品处方 IG 的一部分。

* [处方药品](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [处方覆盖率计划](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>示例 rest 文件

若要协助创建这些搜索参数和配置文件，请在开源站点上创建 [Da Vinci 处方](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) 示例 HTTP 文件，其中包括上述所有步骤。 上载所有必要的配置文件和搜索参数后，可以在 Touchstone 中运行功能声明测试。 应成功运行：

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-script-execution.png" alt-text="Da Vinci 测试脚本执行。":::

## <a name="touchstone-query-test"></a>Touchstone 查询测试

第二个测试是 [查询功能](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)。 此测试验证是否可以使用各种参数搜索特定的覆盖计划和药品资源。 最佳路径将针对你在数据库中已有的资源进行测试，但是，我们还提供了 [Da VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) HTTP 文件，其中包含从 IG 中提取的示例资源，可用于创建资源和测试。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-execution-results.png" alt-text="Da Vinci 测试执行结果。":::

## <a name="next-steps"></a>后续步骤

在本教程中，我们将逐步介绍如何在 Touchstone 中将 Da Vinci 付款人数据传递 Exchange 美国药品处方。 接下来，可以在 Touchstone 中学习如何测试 Da Vinci PDex 实现指南。

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)