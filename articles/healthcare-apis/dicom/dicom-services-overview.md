---
title: DICOM 服务概述 - Azure Healthcare APIs
description: 本文介绍 DICOM、医学成像和 DICOM 服务的概念。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 7cff5c0095bdb9d230673f347cdf5b3e6d099d17
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777812"
---
# <a name="overview-of-the-dicom-service"></a>DICOM 服务概述

> [!IMPORTANT]
> Azure Healthcare APIs 目前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍 DICOM、医学成像和 DICOM 服务的概念。

## <a name="medical-imaging"></a>医学成像

医学成像是创建身体内部结构的视觉表示形式以进行临床分析和医疗介入，以及提供某些器官或组织（生理组织）官能的视觉表示形式的技术和过程。 医学成像旨在揭示皮肤和骨骼遮盖的内部结构，以及诊断和治疗疾病。 医学成像还可建立正常解剖构造和生理组织的数据库，以供医生用于识别异常。 尽管可以出于医疗原因对已切除的器官和组织进行成像，但此类过程通常是病理学而不是医学成像的一部分。 [维基百科，医学成像](https://en.wikipedia.org/wiki/Medical_imaging)

## <a name="dicom"></a>DICOM

DICOM（医学数字成像和通信）是传输、存储、检索、打印、处理和显示医学成像信息的国际标准，并且是医疗保健行业接受的主要医学成像标准。 尽管存在一些例外情况（牙医和兽医），但几乎所有医疗专业机构、设备制造商、软件供应商和个体从业者在任何涉及到成像的医疗工作流的某个阶段，都会依赖 DICOM。 DICOM 确保医学影像符合质量标准，因而可以保证诊断准确性。 大多数成像方式，包括计算机断层扫描 (CT)、磁共振成像 (MRI) 和超声扫描，都必须符合 DICOM 标准。 需要通过专用的 DICOM 应用程序访问和使用 DICOM 格式的影像。

## <a name="dicom-service"></a>DICOM 服务

DICOM 服务是一个托管服务，需要在 Azure Healthcare APIs 工作区中部署 Azure 订阅和 Azure Active Directory 帐户才能使用它。 使用该服务可与任何支持 DICOMweb&trade; 的系统进行基于标准的通信。 DICOM 服务将 DICOM 元数据注入到 FHIR 服务或 FHIR 服务器，从而提供临床数据和成像元数据的单一事实来源。 

有效集成非临床数据的需求变得日益迫切。 为了有效治疗患者、研究新的疗法、诊断解决方案或提供单个患者健康史的有效概述，组织必须集成多个源的数据。 最紧迫的集成之一是临床数据与成像数据之间的集成。

FHIR&trade; 已逐渐成为临床数据的重要标准，它提供扩展性来支持直接或通过引用集成其他类型的数据。 使用 DICOM 服务，组织可以在 FHIR&trade; 中存储对成像数据的引用，并可以跨临床数据集和成像数据集进行查询。 这样可以实现许多不同的方案，例如：

- 创建队列用于研究。 医生经常会查询与临床系统中的数据和成像系统中的数据均匹配的患者，例如：“请提供过去两年内被诊断为骨肉瘤的、年龄超过 45 岁的所有患者的所有处方用药及其 CT 扫描文件和相关放射线检测报告”（这就触发了 FHIR&trade; 和 DICOM 数据的集成工作）。
- 查找类似患者的疗效以了解疗法并安排治疗。 有了患者诊断记录后，医生可以找出以往有类似诊断的患者的疗效和治疗方案，甚至可以参考他们的成像数据。
- 在诊断期间提供患者的纵向视图。 放射科医生（尤其是远程放射科医生）通常无法完全访问患者的医疗病历和相关影像研究结果。 通过 FHIR&trade; 集成可以轻松提供这些数据，甚至还可以提供给组织局域网外部的放射科医生。
- 关闭远程放射科医生的反馈回路。 理想情况下，放射科医生可以访问医院的临床数据，以便在给出建议后关闭反馈回路。 但是，对于远程放射科医生，情况却往往不是这样。 相反，他们在执行诊断后通常无法关闭反馈回路，因为他们在进行初始读取后无法访问患者数据。 由于无法访问临床结果或疗效（或访问受限），他们无法获得所需的反馈来提高技能。 有远程放射科医生提到：“以甲状旁腺为例。 我们在这方面所做的研究比本国任何其他诊所都要多，但我不得不恳求外科医生告知我他们实际发现了什么。 在我每个月展开的 500 多项研究中，只有三四项会得到直接反馈。”  通过与 FHIR&trade; 集成，组织可以轻松创建一个工具来为远程放射科医生提供直接反馈，帮助他们磨练技能并在未来给出更好的建议。
- 关闭 AI/ML 模型的反馈回路。 如果可以使用真实的反馈来改进模型，则机器学习模型能够发挥最好的效果。 但是，第三方 ML 模型提供商极少得到所需的反馈来不断改进其模型。 例如，某家 ISV 这样说：“我们使用了机器模型和人类专家的组合来推荐心脏手术的治疗方案。 然而，极少有医生向我们反馈我们的方案的准确度。 例如，我们经常推荐某个支架大小。 我们希望获得有关预测是否正确的反馈，但是，只有在我们的建议出现重大问题时，客户才会给出反馈。” 与可为远程放射科医生提供反馈一样，与 FHIR&trade; 的集成能让组织创建一个机制来向模型重新训练管道提供反馈。

## <a name="deploy-dicom-service-to-azure"></a>将 DICOM 服务部署到 Azure

DICOM 服务需要有一个 Azure 订阅，用于配置和运行所需的组件。 默认情况下，这些组件是在现有或新的 Azure 资源组内部创建的，以便简化管理。 此外，还需要一个 Azure Active Directory 帐户。 对于每个 DICOM 服务实例，我们将创建隔离资源和多租户资源的组合。

## <a name="summary"></a>摘要

本概念文章概述了 DICOM、医学成像和 DICOM 服务。
 
## <a name="next-steps"></a>后续步骤

若要开始使用 DICOM 服务，请参阅：

>[!div class="nextstepaction"]
>[将 DICOM 服务部署到 Azure](deploy-dicom-services-in-azure.md)

>[!div class="nextstepaction"]
>[将 DICOMweb&trade; 标准 API 与 DICOM 服务配合使用](dicomweb-standard-apis-with-dicom-services.md)
