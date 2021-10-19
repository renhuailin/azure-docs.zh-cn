---
title: '教程 - Da Vinci PDex - Azure 医疗保健 API (预览) '
description: 本教程逐步介绍如何设置 FHIR 服务，以通过 Da Vinci Payer Data Exchange实现指南的测试。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: d4b6906cc646d6dd742c15b78feb8af1e83a56ce
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779851"
---
# <a name="da-vinci-pdex"></a>Da Vinci PDex

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本教程中，我们将演练在 Azure 医疗保健 API (中设置 FHIR 服务（称为 FHIR 服务) ）以通过[Da Vinci Payer Data Exchange Implementation Guide](http://hl7.org/fhir/us/davinci-pdex/toc.html) (PDex IG) 的[Touchstone](https://touchstone.aegis.net/touchstone/)测试。

> [!NOTE]
> 对于所有这些测试，我们将针对 JSON 测试运行它们。 FHIR 服务支持 JSON 和 XML，但它没有单独的终结点来访问 JSON 或 XML。 因此，所有 XML 测试都将失败。 若要在 XML 中查看功能语句，只需传递格式参数 \_ \` ：GET {fhirurl}/metadata？ \_format=xml\`

## <a name="touchstone-capability-statement"></a>Touchstone 功能语句

我们将重点介绍的第一组测试是针对 PDex IG 功能语句测试 FHIR 服务。 这包括三个测试：

* 第一个测试根据 IG 要求验证基本功能语句，并且无需任何更新即可通过。

* 第二个测试验证是否为 US Core 添加了所有配置文件。 此测试无需更新即可通过，但会包含一系列警告。 若要删除这些警告，需要 [加载 US Core 配置文件](validation-against-profiles.md)。 我们创建了一个示例 [HTTP 文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) ，该文件逐步介绍如何创建所有配置文件。 还可以直接从 HL7 站点获取配置文件，该站点将具有最新版本。 [](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles)

* 第三个测试验证是否$patient [一切操作](patient-everything.md) 。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Da Vinci PDex 执行失败。":::

## <a name="touchstone-member-match-test"></a>触控$member匹配测试

Payer [Data](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) Exchange 部分中的第二个测试测试是否存在$member[匹配操作](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)。 可以在 $member 匹配操作概述 中详细了解$member [匹配操作](tutorial-member-match.md)。

在此测试中，需要加载一些示例数据，使测试通过。 我们在此处有一个 [REST 文件](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) ，链接了测试所需的患者和覆盖范围。 加载此数据后，你将能够成功通过此测试。 如果未加载数据，则由于找不到完全匹配，你将收到 422 响应。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="Da Vinci PDex 测试脚本通过。":::

## <a name="touchstone-patient-by-reference"></a>按参考对患者进行触控删除

我们将审查的下一个测试是 [患者参考测试](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) 。 这组测试验证你能否根据各种搜索条件找到患者。 按引用测试患者的最佳方法将是针对你自己的数据进行测试，但我们上传了一个示例资源文件，你可以加载该文件供[](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http)使用。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da Vinci PDex 执行已通过。":::

## <a name="touchstone-patienteverything-test"></a>Touchstone patient/$everything测试

我们将演练的最后一个测试是测试 patient-everything。 对于此测试，需要加载患者，然后使用该患者 ID 来测试能否使用 $everything 操作来拉取与患者相关的所有数据。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="通过触摸板患者/$everything测试。":::

## <a name="next-steps"></a>后续步骤

在本教程中，我们演练了如何在 Touchstone 中通过 Exchange测试。 接下来，可以了解如何测试 Da Vinci PDEX Payer Network (Plan-Net) 实现指南。

>[!div class="nextstepaction"]
>[Da Vinci Plan Net](davinci-plan-net.md)  
