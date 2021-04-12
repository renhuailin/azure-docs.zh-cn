---
title: 转换 ontology
titleSuffix: Azure Digital Twins
description: 了解将行业标准模型转换为适用于 Azure 数字孪生的 DTDL 的过程
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100561376"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>将行业标准本体转换为适用于 Azure 数字孪生的 DTDL

大多数[本体](concepts-ontologies.md)基于语义 Web 标准，例如 [OWL](https://www.w3.org/OWL/)、[RDF](https://www.w3.org/2001/sw/wiki/RDF) 和 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)。 

若要将模型用于 Azure 数字孪生，则其必须采用 DTDL 格式。 本文通过转换模式介绍了将基于 RDF 的模型转换为 DTDL 以使其用于 Azure 数字孪生的通用设计指南。 

本文还包含用于 RDF 和 OWL 转换器的[示例转换器代码](#converter-samples)，可将其扩展以用于建筑行业中的其他架构。

## <a name="conversion-pattern"></a>转换模式

在将基于 RDF 的模型转换为 DTDL 时可以使用多个第三方库。 其中一些库支持将模型文件加载到图形中。 你可以遍历图形查找特定的 RDFS 和 OWL 构造，并将其转换为 DTDL。   

下表举例说明了如何将 RDFS 和 OWL 构造映射到 DTDL。 

| RDFS/OWL 概念 | RDFS/OWL 构造 | DTDL 概念 | DTDL 构造 |
| --- | --- | --- | --- |
| 类 | `owl:Class`<br>IRI 后缀<br>``rdfs:label``<br>``rdfs:comment`` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 对  进行子类化 | `owl:Class`<br>IRI 后缀<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| DataType 属性 | `owl:DatatypeProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:label`<br>`rdfs:range` | 接口属性 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 对象属性 | `owl:ObjectProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | 关系 | `type:Relationship`<br>`name`<br>`target`（如果没有 `rdfs:range` 则省略）<br>`comment`<br>`displayName`<br>

下面的 C# 代码段演示如何使用 [dotNetRDF](https://www.dotnetrdf.org/) 库将 RDF 模型文件加载到图形并转换为 DTDL。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>转换器示例

### <a name="rdf-converter-application"></a>RDF 转换器应用程序 

提供了一个示例应用程序，可将基于 RDF 的模型文件转换为供 Azure 数字孪生服务使用的 [DTDL（版本 2）](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 它已针对 [Brick](https://brickschema.org/ontology/) 架构进行了验证，且可以将其扩展以用于建筑行业中的其他架构（如[构建拓扑本体 (BOT)](https://w3c-lbd-cg.github.io/bot/)、[语义传感器网络](https://www.w3.org/TR/vocab-ssn/)或 [buildingSmart 行业基础类 (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)）。

该示例是一个名为 RdfToDtdlConverter 的 .NET Core 命令行应用程序。

可在此处获取该示例：[RdfToDtdlConverter](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

若要将代码下载到计算机，请单击示例登录页标题下面的“下载 ZIP”按钮。 将下载 RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip 名称下的 ZIP文件，之后你可以进行解压缩和浏览。

你可以使用此示例在上下文中查看转换模式，并将其用作自己的应用程序构建块，根据自己的特定需要执行模型转换。

### <a name="owl2dtdl-converter"></a>OWL2DTDL 转换器 

[OWL2DTDL 转换器](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL)是一个可将 OWL 本体转换为一组 DTDL 接口声明以用于 Azure 数字孪生服务的示例。 它还适用于由一个根本体构成且通过 `owl:imports` 声明重用其他本体的本体网络。

此转换器用于将[房地产核心本体](https://doc.realestatecore.io/3.1/full.html)转换为 DTDL，并且它可用于任何基于 OWL 的本体。

## <a name="next-steps"></a>后续步骤 

* 了解有关扩展行业标准本体以满足要求的详细信息：[概念：扩展行业本体](concepts-ontologies-extend.md)。

* 或者，继续开发基于本体的模型：[在模型开发路径中使用本体策略](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)。