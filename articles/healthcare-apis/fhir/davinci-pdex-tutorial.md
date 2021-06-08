---
title: 教程-Davinci.txt PDex-Azure API for FHIR
description: 本教程介绍如何设置用于 FHIR 的 Azure API，以通过 Da Vinci 付款人数据交换实现指南的测试。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/02/2021
ms.openlocfilehash: 3f9aa795a08aa027fd0cc9758e9479fa0ec81e09
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592975"
---
# <a name="davinci-pdex"></a>Davinci.txt PDex

在本教程中，我们将逐步介绍如何设置 Azure API for FHIR，以便通过[Da Vinci 付款人 Data Exchange 实现指南](http://hl7.org/fhir/us/davinci-pdex/toc.html)的[Touchstone](https://touchstone.aegis.net/touchstone/)测试， (PDex IG) 。

> [!NOTE]
> 对于所有这些测试，我们将针对 JSON 测试运行这些测试。 适用于 FHIR 的 Azure API 支持 JSON 和 XML，但它没有单独的终结点来访问 JSON 或 XML。 因此，所有 XML 测试都将失败。 如果要在 XML 中查看功能语句，只需传递 \_ 格式参数： \` GET {fhirurl}/metadata？ \_格式 = xml\`

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一组测试是针对 PDex IG 功能声明测试适用于 FHIR 的 Azure API。 这包括三项测试：

* 第一次测试针对 IG 要求验证基本功能声明，并将通过而不进行任何更新。

* 第二个测试验证是否已为美国核心添加了所有配置文件。 此测试将通过而不进行更新，但会包含一组警告。 若要删除这些警告，需要 [加载美国核心配置文件](validation-against-profiles.md)。 我们创建了一个 [示例 HTTP 文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) ，指导如何创建所有配置文件。 你还可以直接从 HL7 网站获取 [配置文件](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) ，这将具有最新版本。

* 第三个测试验证是否支持 [$patient 所有操作](patient-everything.md) 。 现在，此测试将失败。 该操作将在用于 FHIR 的 Azure API 中的2021年6月6日推出，现已在 Cosmos DB 上的开源 FHIR 服务器中提供。 但是，功能声明中缺少此测试，因此在我们发布 bug [1989](https://github.com/microsoft/fhir-server/issues/1989)的修复之前，此测试将失败。 

 
:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-failed.png" alt-text="Davinci.txt PDex 执行失败。":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-match 测试

付款人数据交换部分中的 [第二个测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) 测试 [$member 匹配操作](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)是否存在。 你可以在我们的 [$member 匹配操作概述](tutorial-member-match.md)中阅读有关 $member 匹配操作的详细信息。

在此测试中，需要加载一些示例数据，以使测试通过。 [这里](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http)有一个 rest 文件，其中包含用于测试的患者和覆盖面。 加载此数据后，你将能够成功通过此测试。 如果未加载数据，则会收到422响应，因为未找到完全匹配项。

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-passed.png" alt-text="已通过 Davinci.txt PDex 测试脚本。":::

## <a name="touchstone-patient-by-reference"></a>按引用 Touchstone 患者

我们要回顾的下一次测试是 [通过参考](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) 测试的患者。 这组测试将验证你是否可以根据各种搜索条件查找患者。 按引用测试患者的最佳方式是针对自己的数据进行测试，但我们上传了一个可加载的 [示例资源文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) 。

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-execution-passed.png" alt-text="已通过 Davinci.txt PDex 执行。":::

## <a name="touchstone-patienteverything-test"></a>Touchstone 患者/$everything 测试

我们将演练的最后一个测试是测试患者。 对于此测试，你需要加载一个患者，然后将使用该患者的 ID 来测试你是否可以使用 $everything 操作来请求与患者相关的所有数据。

## <a name="next-steps"></a>后续步骤

在本教程中，我们介绍了如何在 Touchstone 中传递付款人 Exchange 测试。 接下来，可以了解适用于 FHIR 的所有 Azure API 功能。

>[!div class="nextstepaction"]
>[受支持的功能](fhir-features-supported.md)  