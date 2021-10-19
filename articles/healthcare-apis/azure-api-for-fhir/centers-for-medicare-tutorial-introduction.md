---
title: 教程-管理局和 Medicaid Services (CMS 的中心) 简介-Azure API for FHIR
description: 本概述介绍了一系列教程，这些教程适用于管理局和 Medicaid 服务 (CMS) 互操作性和患者访问规则的中心。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/05/2021
ms.openlocfilehash: d00d973bb589c7538c098821505dfdb0b10608c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781891"
---
# <a name="centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule-introduction"></a>适用于管理局和 Medicaid 服务 (CMS 的中心) 互操作性和患者访问规则简介

在本系列教程中，我们将介绍管理局和 Medicaid 服务 (的中心的高级摘要) 互操作性和患者访问规则，以及此规则中所述的技术要求。 我们将逐步介绍此规则所引用的各种实现指南。 我们还将提供有关如何配置用于 FHIR 的 Azure API 的详细信息，以支持这些实现指南。


## <a name="rule-overview"></a>规则概述

CMS 在2020年5月1日发布了 [互操作性和患者访问规则](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) 。 此规则需要患者护理 (患者、提供商和支付方) 中涉及的所有参与方之间的免费安全数据流，以允许患者在需要时访问其健康信息。 互操作性已在几十年中困扰了医疗保健行业，导致了孤立的数据导致消极的运行状况结果，并具有更高和不可预测的质量。 CMS 使用其机构来控制管理局优势 (MA) 、Medicaid、儿童的健康保险计划 (芯片) 和合格的健康计划 (QHP) 颁发者 (上) 的颁发者 FFEs 来实施此规则。 

在2020年8月，CMS 详细说明了组织如何满足此要求。 若要确保数据可以安全地交换并以标准化方式进行交换，则已标识 FHIR 版本 4 (R4) 为数据交换所需的基础标准。 

互操作性和患者访问部分分为三个主要部分：

* **患者访问 API (需要2021年7月1日)** – (如上文所定义的那样需要) 实现和维护基于标准的安全 API，该 api 允许患者通过其所选的第三方应用程序轻松访问其声明并遇到信息（包括成本）和已定义的部分临床信息。  

* **提供程序目录 API (需要2021年7月1日)** –此部分规则需要 CMS 管控的支付方，以使提供程序目录信息通过基于标准的 API 公开提供。 通过使此信息可用，第三方应用程序开发人员将能够创建服务，以帮助患者查找提供商，以满足特定的护理需求，而临床医生查找其他提供程序以进行护理协调。  

* 支付 **给付款人的数据 Exchange (需要2022年1月1日)** –需要在与其他支付方进行患者请求时交换某些患者临床数据。 尽管不需要遵循任何一种标准，但建议应用 FHIR 来交换此数据。 

## <a name="key-fhir-concepts"></a>关键 FHIR 概念

如上所述，需要 FHIR R4 才能满足此要求。 此外，还开发了若干为规则提供指导的实施指南。 [实现指南](https://www.hl7.org/fhir/implementationguide.html) 在基本 FHIR 规范的基础上提供了额外的上下文。 这包括定义其他搜索参数、配置文件、扩展、操作、值集和代码系统。

适用于 FHIR 的 Azure API 具有以下功能，可帮助你为各种实现指南配置数据库：

* [支持 RESTful 交互](fhir-features-supported.md)
* [存储和验证配置文件](validation-against-profiles.md)
* [定义和索引自定义搜索参数](how-to-do-custom-search.md)
* [转换数据](convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>患者访问 API 实现指南

患者访问 API 介绍了以下四个 FHIR 实现指南：

* [CARIN IG®的蓝色按钮](http://hl7.org/fhir/us/carin-bb/STU1/index.html)：根据的 CARIN IG 的 ""，要求支付方提出患者的索赔并获得数据 (C4BB IG) 。 C4BB IG 提供了一组可通过 FHIR API 向使用者显示的资源，其中包括互操作性和患者访问 API 中声明数据所需的详细信息。 此实现指南使用 ExplanationOfBenefit (EOB) 资源作为主资源，在引用其他资源时将其拉入。
* [HL7 FHIR Da Vinci PDex IG](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html)：付款人 Data Exchange 实现指南 (PDex IG) 重点确保支付了所有相关的患者临床数据，以满足患者访问 API 的要求。 这会在 R4 资源上使用 US 核心配置文件，其中包含 (最少) 遇到、提供程序、组织、位置、服务日期、诊断、过程和观察日期。 尽管这些数据可能以 FHIR 格式提供，但它也可能来自其他系统，格式为声明数据、HL7 V2 消息和 CDA 文档。
* [HL7 Us CORE IG](https://www.hl7.org/fhir/us/core/toc.html)： HL7 美国核心指南 (美国核心 IG) 是上述 PDex IG 的主干。 虽然 PDex IG 限制了某些资源，甚至超出了美国核心 IG，但许多资源只需遵循美国核心 IG 中的标准。

* [HL7 FHIR Da Vinci-PDEX 美国药品处方 IG](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html)： Part D 管理局优势计划必须通过患者 API 提供处方信息。 他们使用 PDex 美国药品处方实现指南 (USDF IG) 执行此操作。 USDF IG 定义一个 FHIR 接口，用于运行状况保险公司的药品处方信息，这是运行状况保险公司同意支付的品牌名称和通用处方药品的列表。 这种情况的主要用例是，患者可以了解是否有可用于其规定的备用药品，并对药品成本进行比较。

## <a name="provider-directory-api-implementation-guide"></a>提供程序目录 API 实现指南

提供程序目录 API 介绍了一个实现指南：

* [HL7 Da Vinci PDex 计划网络 IG](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/)：此实施指南定义了一个 FHIR 接口，用于运行状况保险公司的保险计划、其关联网络以及参与这些网络的组织和提供商。

## <a name="touchstone"></a>Touchstone

若要测试各种实现指南， [Touchstone](https://touchstone.aegis.net/touchstone/) 是一种非常有用的资源。 在即将推出的教程中，我们将重点介绍如何确保将适用于 FHIR 的 Azure API 配置为成功通过各种 Touchstone 测试。 Touchstone 站点提供了许多有用的文档，可帮助你启动并运行。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解互操作性和患者访问规则、实现指南和可用的测试工具 (Touchstone) ，我们将逐步介绍如何设置用于 FHIR 的 Azure API，以用于的 CARIN IG。 

>[!div class="nextstepaction"]
>[CARIN Blue Button 实施指南](../fhir/carin-implementation-guide-blue-button-tutorial.md)  