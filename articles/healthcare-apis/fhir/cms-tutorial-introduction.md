---
title: 教程 - 且 Medicdic Services 中心 (CMS) 简介 - Azure API for FHIR
description: 本概述介绍一系列教程，这些教程与 CMS (与患者) 和患者访问规则相关。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 05/27/2021
ms.openlocfilehash: 885fa84c19b5f0e3c5b4aa32d4aa3a230033b2f6
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592861"
---
# <a name="centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule-introduction"></a>CMS 的 一般 (中心) 互操作性和患者访问规则简介

在本系列教程中，我们将介绍 一个高级摘要，其中概述了 用于 一般服务和医疗服务中心 (CMS) 互操作性和患者访问规则，以及此规则中概述的技术要求。 我们将演练此规则引用的各种实现指南。 我们还将详细介绍如何配置应用程序Azure API for FHIR这些实现指南。


## <a name="rule-overview"></a>规则概述

CMS 于[](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index)2020 年 5 月 1 日发布了互操作性和患者访问规则。 此规则要求患者、提供者和支付方 (参与方之间免费且安全的数据流) 以允许患者在需要时访问其健康信息。 几十年来，互操作性一直使医疗保健行业变得孤立，导致负面健康结果，并且医疗保健成本更高且不可预测。 CMS 正使用其授权在联邦联合促进交换 (FFE) 上监管 且拥有 且具有资格的医疗保健计划 (MA) 、Medic一、Children's Health Insurance Program (CHIP) 和合格健康计划 (QHP) 颁发者，以强制实施此规则。 

2020 年 8 月，CMS 详细介绍了组织如何满足该任务。 为了确保以标准化方式安全地交换数据，CMS 将 FHIR 版本 4 (R4) 标识为数据交换所需的基础标准。 

互操作性和患者访问服务有三个主要部分：

* 患者访问 API (必需于 **2021 年 7** 月 1 日) – 需要 CMS 监管的支付方 (（如上面定义的) ）来实现和维护一个安全的、基于标准的 API，使患者能够轻松访问其声明和遇到信息（包括成本），并通过他们选择的第三方应用程序获得其定义的临床信息子集。  

* 提供程序目录 **API (2021 年 7** 月 1 日) - 此规则部分要求由 CMS 监管的支付者，才能通过基于标准的 API 公开提供程序目录信息。 通过提供此信息，第三方应用程序开发人员将能够创建服务，帮助患者查找满足特定护理需求的提供者，而临床医生将查找其他提供者来协调护理。  

* 付费方到支付方数据交换 (需要 **于 2022** 年 1 月 1 日) - CMS 监管的支付方需要根据患者的请求与其他支付方交换某些患者临床数据。 尽管无需遵循任何类型的标准，但建议应用 FHIR 来交换此数据。 

## <a name="key-fhir-concepts"></a>FHIR 关键概念

如上所述，需要 FHIR R4 才能满足此要求。 此外，还开发了多个实现指南，为规则提供指导。 [实现指南](https://www.hl7.org/fhir/implementationguide.html) 在基本 FHIR 规范的顶层提供额外的上下文。 这包括定义其他搜索参数、配置文件、扩展、操作、值集和代码系统。

此Azure API for FHIR具有以下功能，可帮助你为各种实现指南配置数据库：

* [支持 RESTful 交互](fhir-features-supported.md)
* [存储和验证配置文件](validation-against-profiles.md)
* [定义自定义搜索参数并编制索引](how-to-do-custom-search.md)
* [转换数据](convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>患者访问 API 实现指南

患者访问 API 介绍了遵守四个 FHIR 实施指南：

* 蓝色按钮的[CARIN IG®：](http://hl7.org/fhir/us/carin-bb/STU1/index.html)根据 CARIN IG for Blue Button Implementation Guide (C4BB IG) （蓝色按钮实现指南 (C4BB IG) ）要求支付方提供患者申请和遇到数据。 C4BB IG 提供一组资源，支付者可以通过 FHIR API 向使用者显示这些资源，并包含互操作性和患者访问 API 中声明数据所需的详细信息。 本实现指南使用 ExplanationOfBenefit (EOB) Resource 作为主资源，在引用其他资源时拉取这些资源。
* [HL7 FHIR Da Vinci PDex IG：](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html)支付方数据交换实施指南 (PDex IG) 侧重于确保支付方提供所有相关患者临床数据，以满足患者访问 API 的要求。 这会在 R4 资源上使用 US Core 配置文件，并且 (至少) 遇到、提供程序、组织、位置、服务日期、诊断、过程和观察结果。 尽管此数据可能以 FHIR 格式提供，但它也可能来自声明数据、HL7 V2 消息和 C-CDA 文档格式的其他系统。
* [HL7 US Core](https://www.hl7.org/fhir/us/core/toc.html)IG：HL7 US Core 实施指南 (US Core IG) 是上述 PDex IG 的主干。 虽然 PDex IG 限制某些资源甚至比美国核心 IG 还多，但许多资源仅遵循美国核心 IG 中的标准。

* [HL7 FHIR Da Vinci - PDex US Formulary IG：](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html)第 D 部分 使用优势计划必须使公式信息通过患者 API 提供。 他们使用 PDex US Formulary 实现指南 (USDF IG) 。 USDF IG 定义一个 FHIR 接口，该接口用于访问健康机构药物公式信息，该接口是一个品牌名称和一般药物列表，健康医生同意支付这些费用。 这样做的主要用例是使患者能够了解是否对已规定药物可用的替代药物，并比较药物成本。

## <a name="provider-directory-api-implementation-guide"></a>提供程序目录 API 实现指南

提供程序目录 API 描述遵循一个实现指南：

* [HL7 Da Vinci PDex Plan Network IG：](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/)此实施指南定义了一个 FHIR 接口，用于连接健康机构保险计划、其关联网络以及参与这些网络的组织和提供商。

## <a name="touchstone"></a>试金石

为了测试对各种实现指南的遵循情况 [，Touchstone](https://touchstone.aegis.net/touchstone/) 是一个很好的资源。 在即将推出的教程中，我们将重点介绍如何确保将 Azure API for FHIR 配置为成功通过各种 Touchstone 测试。 Touchstone 站点有许多很好的文档，可帮助你启动并运行。

## <a name="next-steps"></a>后续步骤

现在你已基本了解互操作性和患者访问规则、实施指南和可用的测试工具 (Touchstone) ，我们将演练如何为 BLUE 按钮的 CARIN IG 设置 Azure API for FHIR。 

>[!div class="nextstepaction"]
>[蓝色按钮的 CARIN 实现指南](carin-implementation-guide-blue-button-tutorial.md)  