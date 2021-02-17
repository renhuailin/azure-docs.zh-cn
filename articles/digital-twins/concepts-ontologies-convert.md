---
title: 转换本体论
titleSuffix: Azure Digital Twins
description: 了解将行业标准模型转换为 Azure 数字孪生的 DTDL 的过程
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561376"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>将行业标准本体论转换为 Azure 数字孪生的 DTDL

大多数 [本体论](concepts-ontologies.md) 基于语义 web 标准，例如 [OWL](https://www.w3.org/OWL/)、 [RDF](https://www.w3.org/2001/sw/wiki/RDF)和 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)。 

若要将模型用于 Azure 数字孪生，它必须采用 DTDL 格式。 本文介绍了一种 **转换模式** 的一般设计指南，用于将基于 RDF 的模型转换为 DTDL，以便将其与 Azure 数字孪生结合使用。 

本文还包含用于 RDF 和 OWL 转换器的 [**示例转换器代码**](#converter-samples) ，可对建筑行业中的其他架构进行扩展。

## <a name="conversion-pattern"></a>转换模式

可以使用多个第三方库，在将基于 RDF 的模型转换为 DTDL 时可以使用它们。 其中一些库允许您将模型文件加载到图形中。 您可以循环遍历图形查找特定的 RDFS 和 OWL 构造，并将其转换为 DTDL。   

下表举例说明了如何将 RDFS 和 OWL 构造映射到 DTDL。 

| RDFS/OWL 概念 | RDFS/OWL 构造 | DTDL 概念 | DTDL 构造 |
| --- | --- | --- | --- |
| 类 | `owl:Class`<br>IRI 后缀<br>``rdfs:label``<br>``rdfs:comment`` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 对  进行子类化 | `owl:Class`<br>IRI 后缀<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| 数据类型属性 | `owl:DatatypeProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:label`<br>`rdfs:range` | 接口属性 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 对象属性 | `owl:ObjectProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | 关系 | `type:Relationship`<br>`name`<br>`target`如果没有) ，则 (或省略 `rdfs:range`<br>`comment`<br>`displayName`<br>

下面的 c # 代码段演示如何使用 [**dotNetRDF**](https://www.dotnetrdf.org/) 库将 RDF 模型文件加载到图形并转换为 DTDL。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>转换器示例

### <a name="rdf-converter-application"></a>RDF 转换器应用程序 

有一个可用的示例应用程序，它将基于 RDF 的模型文件转换为 [DTDL (版本 2) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) ，以供 Azure 数字孪生服务使用。 它已针对程序 [块](https://brickschema.org/ontology/) 架构进行了验证，可针对建筑行业 (中的其他架构进行扩展，如 [构建拓扑 Ontology (机器人) ](https://w3c-lbd-cg.github.io/bot/)、 [语义传感器网络](https://www.w3.org/TR/vocab-ssn/)或 [buildingSmart 行业基础类 (IFC) ](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)) 。

该示例是一个名为 **RdfToDtdlConverter** 的 .net Core 命令行应用程序。

可在此处获取示例： [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

若要将代码下载到你的计算机，请点击示例登录页上标题下面的 " *下载 ZIP* " 按钮。 这会下载名称 *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* 下的 *ZIP* 文件，然后你可以对其进行解压缩和浏览。

您可以使用此示例来查看上下文中的转换模式，还可以将其用作自己的应用程序的构建基块，根据自己的特定需要执行模型转换。

### <a name="owl2dtdl-converter"></a>OWL2DTDL 转换器 

[**OWL2DTDL 转换器**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL)是一个示例，该示例将 OWL ontology 转换为一组 DTDL 接口声明，这些声明可与 Azure 数字孪生服务一起使用。 它还适用于 ontology 网络，由一个根 ontology 通过声明重复使用其他本体论 `owl:imports` 。

此转换器用于将房地产 [Core Ontology](https://doc.realestatecore.io/3.1/full.html) 转换为 DTDL，可用于任何基于 OWL 的 Ontology。

## <a name="next-steps"></a>后续步骤 

* 详细了解如何扩展行业标准本体论以满足你的规范： [*概念：扩展行业本体论*](concepts-ontologies-extend.md)。

* 或者，继续查看基于本体论开发模型的路径： [*在模型开发路径中使用 ontology 策略*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)。