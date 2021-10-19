---
title: 有关 Azure Healthcare APIs 中 FHIR 服务的常见问题解答
description: 获取有关 FHIR 服务的常见问题解答，例如 FHIR API 数据的存储位置和版本支持。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.custom: references_regions
ms.openlocfilehash: 7e36e91125e721d8ce5ed4dbc2603109838ca531
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122780004"
---
# <a name="frequently-asked-questions-about-the-fhir-service"></a>FHIR 服务常见问题解答

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本部分介绍有关 Azure 医疗保健 Api FHIR 服务的一些常见问题， (特此称为 FHIR service) 。

## <a name="fhir-service-the-basics"></a>FHIR 服务：基础知识

### <a name="what-is-fhir"></a>什么是 FHIR？

快速医疗保健互操作性资源（FHIR - 发音为“fire”）是一个互操作性标准，旨在允许在不同的健康状况系统之间交换医疗保健数据。 此标准由 HL7 组织开发。如今，世界各地的医疗保健组织均正在采用此标准。 FHIR 的最新可用版本为 R4（版本 4）。 FHIR 服务支持版本 R4，还支持上一版本 STU3（试用标准 3）。 如需获取 FHIR 的详细信息，请访问 [HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR API 数据是否存储在 Azure 中？

是的，数据存储在 Azure 的托管数据库中。 Azure Healthcare APIs 中的 FHIR 服务无法提供对基础数据存储的直接访问。

### <a name="what-identity-provider-do-you-support"></a>支持哪些标识提供者？

目前只支持 Microsoft Azure Active Directory。

### <a name="what-fhir-version-do-you-support"></a>支持哪些 FHIR 版本？

支持版本 4.0.0 和 3.0.1。

有关详细信息，请参阅[支持的功能](fhir-features-supported.md)。 在 [HL7 FHIR 的版本历史记录](https://hl7.org/fhir/R4/history.html)中查看各个 FHIR 版本（例如 STU3 和 R4 之间）间的更改情况。

### <a name="what-is-the-difference-between-the-azure-api-for-fhir-and-the-fhir-service-in-the-healthcare-apis"></a>Azure API for FHIR 与 Azure Healthcare APIs 中的 FHIR 服务有什么区别？

FHIR 服务是对 Azure Healthcare APIs 中 FHIR 规范的实现，此服务可在单个工作区内提供 FHIR 服务和 DICOM 服务。 Azure API for FHIR 是初始正式发布产品，且仍将作为独立产品供用户使用。 其中的主要区别如下：

* FHIR 服务的目前支持大小为 4TB 数据量的数据，且目前为公共预览版；而 Azure API for FHIR 则支持超过 4TB 数据量的数据，且已正式发布。
* FHIR 服务支持[事务捆绑包](https://www.hl7.org/fhir/http.html#transaction)。
* Azure API for FHIR 拥有更多平台功能（例如专用链接、客户托管的密钥和日志记录），而 Azure Healthcare APIs 中的 FHIR 服务尚未推出这些功能。 有关这些功能的更多详细信息，将在正式发布后公布。

### <a name="whats-the-difference-between-fhir-service-in-the-azure-healthcare-apis-and-the-open-source-fhir-server"></a>“Azure Healthcare APIs 中的 FHIR 服务”与开源“FHIR 服务器”之间有什么区别？

Azure Healthcare APIs 中的 FHIR 服务是开源 Microsoft FHIR Server for Azure 的托管版本。 Microsoft 会在托管服务中提供所有维护和更新。

运行 FHIR Server for Azure 时，可以直接访问基础服务。但如需存储 PHI 数据，则需负责维护和更新服务器及所有必需的合规性工作。

### <a name="in-which-regions-is-the-fhir-service-available"></a>可在哪些区域使用 FHIR 服务？

我们正根据客户需求持续扩大 Healthcare API 在全球的覆盖范围。 目前可在以下 25 个区域（包括 2 个美国政府区域）使用 FHIR 服务：澳大利亚东部、巴西南部 *、加拿大中部、印度中部*、东亚 *、美国东部 2、美国东部、德国北部 **、德国中西部、德国北部**、日本东部、日本西部*、韩国中部*、美国中北部、欧洲北部、南非北部、美国中南部、东南亚、瑞士北部、英国南部、英国西部、美国中西部、西欧、美国西部 2、US Gov 弗吉尼亚州、US Gov 亚利桑那州。

*表示个人预览版适用区域；**表示此区域仅适用灾难恢复版。

有关详细信息，请参阅[地理区域支持](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=all)。

### <a name="where-can-i-see-what-is-releasing-into-the-fhir-service"></a>可以在何处查看 FHIR 服务已发布的内容？

若要了解 FHIR 服务已发布的内容，请参阅已[发布](https://github.com/microsoft/fhir-server/releases)的开源 FHIR 服务器。 项目准备发布至托管服务后，我们会一直使用 FHIR 服务标记这些内容，并且这些项目通常在以开源方式存在于发布页面上两周后便可使用。 如果你想要在自己的环境中进行测试，我们还在[此处](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md)提供了有关如何测试生成的说明。 我们正在评估如何以最佳方式共享其他托管服务更新。

### <a name="what-is-smart-on-fhir"></a>什么是 SMART on FHIR？

SMART（可替换式医疗应用程序和可重用技术）on FHIR 是一组开放式规范，用于将合作伙伴应用程序与 FHIR 服务器和其他健康 IT 系统（如电子健康记录和健康信息交换）集成。 通过创建 SMART on FHIR 应用程序，可确保多种不同系统能够访问和使用你的应用程序。
若要详细了解 SMART，请访问 [SMART 健康 IT](https://smarthealthit.org/)。

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>在何处可以找到目前在数据库上运行的 FHIR 版本。

可在“fhirVersion”属性（FHIR URL/元数据）下找到已公开在功能语句中的确切 FHIR 版本

## <a name="fhir-implementations-and-specifications"></a>FHIR 实现和规范

### <a name="can-i-create-a-custom-fhir-resource"></a>能否创建自定义 FHIR 资源？

我们不允许用户创建自定义 FHIR 资源。 如需自定义 FHIR 资源，可在带有扩展的[基本资源](http://www.hl7.org/fhir/basic.html)上生成自定义资源。 

### <a name="are-extensions-supported-on-the-fhir-service"></a>FHIR 服务是否支持[扩展](https://www.hl7.org/fhir/extensibility.html)？

允许将任何有效的 FHIR JSON 数据加载到服务器。 如需存储定义扩展的结构定义，可以将其另存为结构定义资源。 若要搜索扩展，需要[自定义搜索参数](how-to-do-custom-search.md)。 

### <a name="what-is-the-limit-on-_count"></a>对 _count 有何限制？

当前对 _count 的限制为 1000 条。 如果将 _count 的数量设置超过 1000 条，则捆绑包会显示一条警告，提示系统只会显示前 1000 条记录。

### <a name="can-i-post-a-bundle-to-the-fhir-service"></a>能否在 FHIR 服务中发布捆绑包？

目前支持在 FHIR 服务中发布[批量捆绑包](https://www.hl7.org/fhir/valueset-bundle-type.html)和[事务捆绑包](https://www.hl7.org/fhir/http.html#transaction)。

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-fhir-service"></a>该如何在 FHIR 服务中获取单个患者的所有资源？

FHIR 服务支持 [$patient-everything 操作](patient-everything.md)，该操作可提供与单个患者相关的所有数据。 

## <a name="using-the-fhir-service"></a>使用 FHIR 服务

### <a name="where-can-i-see-some-examples-of-using-the-fhir-service-within-a-workflow"></a>在何处可以看到某些在工作流中使用 FHIR 服务的示例？

[健康体系结构 GitHub 页面](https://github.com/microsoft/health-architectures)列有一系列参考体系结构。

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-fhir-service"></a>在何处可以看到将 Web 应用连接到 FHIR 服务的示例？

[健康体系结构 GitHub 页面](https://aka.ms/health-architectures)包含相关示例应用程序和方案，可供参考。 此页面介绍了将 Web 应用连接到 FHIR 服务的方法。
