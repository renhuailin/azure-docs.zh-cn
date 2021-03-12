---
title: 有关 Azure 中的 FHIR services 的常见问题解答-Azure API for FHIR
description: 获取有关用于 FHIR 的 Azure API 的常见问题的解答，如 FHIR Api 和版本支持的数据存储位置。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 22d9df80bce6e79f0058567f8df76935e431e598
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017939"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>有关用于 FHIR 的 Azure API 的常见问题

## <a name="azure-api-for-fhir-the-basics"></a>用于 FHIR 的 Azure API：基础知识

### <a name="what-is-fhir"></a>什么是 FHIR？
 (FHIR 的快速医疗保健互操作性资源 ) 是一个互操作性标准，旨在允许在不同的运行状况系统之间交换医疗保健数据。 此标准由 HL7 组织开发，由世界各地的医疗保健组织使用。 最新版本的 FHIR 是 R4 (版本 4) 。 适用于 FHIR 的 Azure API 支持 R4，并且还支持早期版本 STU3 (Standard for 试用版 3) 。 有关 FHIR 的详细信息，请访问 [HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 是否在 Azure 中存储的数据？

是的，数据存储在 Azure 中的托管数据库中。 适用于 FHIR 的 Azure API 不提供对基础数据存储区的直接访问。

### <a name="what-identity-provider-do-you-support"></a>你支持哪些标识提供程序？

目前支持 Microsoft Azure Active Directory 作为标识提供者。

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>对于 FHIR 的 Azure API，恢复点目标是什么 (RPO) ？
Cosmos DB 作为永久性提供程序，为 FHIR 的 Azure API 提供支持。 因此，服务的 RPO 等于 [Cosmos DB (单区域) ](../../cosmos-db/consistency-levels.md) ，并 < 240 分钟。

### <a name="what-fhir-version-do-you-support"></a>你支持哪个 FHIR 版本？

我们支持 Azure API for FHIR (PaaS) 和 FHIR Server for Azure (开源) 版本4.0.0 和3.0.1。

有关详细信息，请参阅 [支持的功能](fhir-features-supported.md)。 阅读 [HL7 FHIR 的版本历史记录](https://hl7.org/fhir/R4/history.html)中 FHIR 版本之间发生了哪些更改 (即 STU3 到 R4) 。

适用于 FHIR () 预览版的 Azure IoT 连接器目前仅支持 FHIR 版本 R4，并且仅在适用于 FHIR 的 Azure API R4 实例上可见。

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>"用于 Azure 的 Microsoft FHIR 服务器" 和 "用于 FHIR 的 Azure API" 之间有何区别？

适用于 FHIR 的 Azure API 是 Azure 的开源 Microsoft FHIR 服务器托管和托管版本。 在托管服务中，Microsoft 提供所有维护和更新。 

当你运行适用于 Azure 的 FHIR 服务器时，你可以直接访问基础服务，但负责维护和更新服务器以及所有必需的符合性工作（如果你要存储 PHI 数据）。

对于开发观点，每个不适用于托管服务的功能都首先部署到开源 Microsoft FHIR Server for Azure。 在开源中验证后，它将发布到 PaaS Azure API for FHIR 解决方案。 在开源和 PaaS 版本之间的时间取决于功能的复杂性以及其他路线图优先级。 对于我们的所有服务，此过程都是相同的，例如适用于 FHIR (preview) 的 Azure IoT 连接器。

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>在哪里可以看到哪些内容正在发布到 Azure API for FHIR？

若要查看其中一些内容正在发布到用于 FHIR 的 Azure API，请参阅开源 FHIR 服务器的 [版本](https://github.com/microsoft/fhir-server/releases) 。 从2020年11月开始，如果开源项将发布到托管服务，则已使用 Azure API FHIR 标记项。 这些功能通常可在两周内提供，在开放源代码的发布页面上。 https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md)如果要在自己的环境中进行测试，还包括如何测试生成 [此处] (的说明。 我们正在评估如何最好地与其他托管服务更新共享。

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>Azure API for FHIR 提供哪些区域？

目前，我们在 [多个地理区域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)提供公共和政府版的公开上市。 有关 Microsoft 的政府云服务的信息，请参阅 [FedRAMP 的 Azure 服务](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md)。

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>在哪里可以看到哪些内容正在发布到 Azure API for FHIR？

若要查看其中一些内容正在发布到用于 FHIR 的 Azure API，请参阅开源 FHIR 服务器的 [版本](https://github.com/microsoft/fhir-server/releases) 。 如果项将发布到托管服务，并使用 FHIR 标记项，则这些项将发布到托管服务，并且在开放源代码的发布页上通常会有两周可用。 如果要在自己的环境中进行测试，还会在 [此处](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) 提供有关如何测试生成的说明。 我们正在评估如何最好地与其他托管服务更新共享。

### <a name="what-is-smart-on-fhir"></a>什么是 FHIR 的智能？

智能 (可在 FHIR 上替换医疗应用程序和可重用技术) ，这是一套开放式规范，可将合作伙伴应用程序与 FHIR 服务器和其他运行状况 IT 系统（如电子健康记录和健康信息交换）集成。 通过创建智能的 FHIR 应用程序，你可以确保你的应用程序可以通过不同系统的很多进行访问和利用。
用于 FHIR 的身份验证和 Azure API。 若要了解有关智能的详细信息，请访问 [智能运行状况](https://smarthealthit.org/)。

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>在哪里可以找到数据库上运行的 FHIR 的版本。 

您可以在 "fhirVersion" 属性下的 "功能" 语句中找到公开的准确 FHIR 版本。

## <a name="fhir-implementations-and-specifications"></a>FHIR 实现和规范

### <a name="can-i-create-a-custom-fhir-resource"></a>能否创建自定义 FHIR 资源？

我们不允许自定义 FHIR 资源。 如果需要自定义 FHIR 资源，可以在包含扩展的 [基本资源](http://www.hl7.org/fhir/basic.html) 之上构建自定义资源。 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Azure API for FHIR 是否支持 [扩展](https://www.hl7.org/fhir/extensibility.html) ？

我们允许你将任何有效的 FHIR JSON 数据加载到服务器中。 如果要存储定义扩展的结构定义，则可以将其另存为结构定义资源。 当前无法搜索扩展。

### <a name="what-is-the-limit-on-_count"></a>_Count 的限制是多少？

_Count 的当前限制为100。 如果将 _count 设置为超过100，则在捆绑包中将收到一条警告，只会显示100条记录。

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>组导出功能是否有任何限制？

对于组导出，我们只导出组中包含的引用，而不是 [组资源](https://www.hl7.org/fhir/group.html)的所有特征。

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>是否可以将捆绑发布到 Azure API for FHIR？

我们目前支持发布 [批处理捆绑包](https://www.hl7.org/fhir/valueset-bundle-type.html) ，但不支持在用于 FHIR 的 Azure API 中发布事务捆绑。 你可以使用由 SQL 支持的开源 FHIR 服务器来发布事务包。

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>如何在用于 FHIR 的 Azure API 中获取单个患者的所有资源？

我们支持在用于 FHIR 的 Azure API 中 [搜索隔离舱](https://www.hl7.org/fhir/compartmentdefinition.html) 。 这允许你获取与特定患者相关的所有资源。 请注意，现在的隔离舱包含与患者相关的所有资源，但不包括患者本身，因此，如果您的结果中需要患者资源，还需要搜索以获取患者。

下面是此操作的一些示例：

* 获取患者/<id>/*
* 获取患者/ <id> /Observation
* 获取患者/ <id> /Observation？代码 = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中搜索资源时的默认排序方式是什么？

我们支持按上次更新日期排序： _sort = _lastUpdated。 有关其他受支持的搜索参数的详细信息，请查看我们的 [支持功能页](fhir-features-supported.md#search)。

### <a name="does-the-azure-api-for-fhir-support-everything"></a>Azure API for FHIR 是否支持 $everything？ 

否。 目前，我们不支持 $everything。 不过，可以通过两个 API 调用来实现此目的。 例如，若要获取患者 $ 一切，你可以先使用/Patient/[ID] 抓取患者记录，然后使用/Patient/[ID]/* 再次调用来检索所有患者数据。

可以在此 [社区文章](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type)中查看更多详细信息。 

### <a name="how-does-export-work"></a>$Export 是如何工作的？

$export 是 FHIR 规范的一部分： https://hl7.org/fhir/uv/bulkdata/export/index.html 。 如果使用托管标识和存储帐户配置了 FHIR 服务，并且托管标识有权访问该存储帐户，则只需在 FHIR API 上调用 $export，并将所有 FHIR 资源导出到存储帐户。 有关详细信息，请查看 [$export 上的文章](export-data.md)。

### <a name="is-de-identified-export-available-at-patient-and-group-level-as-well"></a>是否也可以从患者和组级别取消识别的导出？
目前仅支持匿名导出 (/$export) ，不适用于患者导出 (/Patient/$export) 。 我们正在努力使其在患者级别可用。

## <a name="using-azure-api-for-fhir"></a>使用用于 FHIR 的 Azure API

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>如何实现为适用于 FHIR 的 Azure API 启用 log analytics？

启用诊断日志记录并允许查看这些日志的示例查询。 有关启用审核日志和示例查询的详细信息，请参阅 [此部分](enable-diagnostic-logging.md)。 如果要在日志中包含其他信息，请 [使用自定义 HTTP 标头](use-custom-headers.md)查看。

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>在何处可查看在工作流中使用 Azure API for FHIR 的一些示例？

我们有一系列参考体系结构，它在 [运行状况体系结构 GitHub 页](https://github.com/microsoft/health-architectures)上提供。

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>在哪里可以看到将 web 应用程序连接到 Azure API for FHIR 的示例？

我们有一个 [运行状况体系结构 GitHub 页面](https://aka.ms/health-architectures) ，其中包含示例应用程序和方案。 它说明了如何将 web 应用程序连接到 Azure API for FHIR。  

## <a name="azure-api-for-fhir-features-and-services"></a>用于 FHIR 的 Azure API 功能和服务 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>是否可以使用我的个人密钥而不是默认密钥来加密我的数据？

是的，Azure API for FHIR 允许配置客户管理的密钥，利用 Cosmos DB 的支持。 有关使用个人密钥加密数据的详细信息，请参阅 [此部分](customer-managed-key.md)。

## <a name="azure-api-for-fhir-preview-features"></a>用于 FHIR 的 Azure API：预览功能

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>能否为适用于 FHIR 的 Azure IoT Connector 配置扩展容量 (预览) ？

由于适用于 FHIR 的 Azure IoT 连接器在公共预览版期间免费，因此其缩放容量是固定的，并受到限制。 公共预览版中提供的适用于 FHIR 配置的 Azure IoT 连接器应提供约200消息/秒的吞吐量。 某些形式的资源容量配置将在公开上市 (公开) 中可用。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>为什么在 Azure API for FHIR 上启用了 "专用" 链接时，无法安装适用于 FHIR 的 Azure IoT 连接器 (预览) ？

Azure IoT Connector for FHIR 目前不支持私有链接功能。 因此，如果在用于 FHIR 的 Azure API 上启用了 Private Link，则无法为 FHIR 安装 Azure IoT 连接器，反之亦然。 当 Azure IoT Connector for FHIR 适用于公开上市 (GA) 时，此限制应消失。
