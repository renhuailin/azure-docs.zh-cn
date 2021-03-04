---
title: 采用行业标准 ontology
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生可采用的现有行业本体论
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124221"
---
# <a name="adopting-an-industry-ontology"></a>采用行业 ontology

由于使用开源 DTDL ontology （而不是从空白页开始）可以更轻松地开始，Microsoft 将与域专家合作发布本体论，这表示广泛接受的行业约定，并支持各种客户用例。 

结果是一组基于开源 DTDL 的本体论，可从中进行学习、构建、学习或直接使用行业标准。 本体论旨在满足下游开发人员的需求，并且可能由行业广泛采用和/或扩展。

目前，Microsoft 与合作伙伴合作开发 [智能建筑](#realestatecore-smart-building-ontology) 的 ontology 和 [智能城市](#smart-cities-ontology)的 ontology，这为基于这些行业中的标准进行建模提供了共同的基础，以避免 reinvention。 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore 智能生成 ontology

在 *此处查找 ontology：[**用于智能建筑物的数字孪生定义语言的 RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)*。

Microsoft 与 [RealEstateCore](https://www.realestatecore.io/)（一家房地产物主、软件供应商和研究机构）合作，为房地产行业提供此开源 DTDL ontology。

此智能建筑 ontology 使用行业标准 (如程序 [块架构](https://brickschema.org/ontology/) 或 [W3C 构建拓扑 ontology](https://w3c-lbd-cg.github.io/bot/index.html)) 来模拟智能建筑物，以避免 reinvention。 Ontology 还附带了有关如何使用和正确地进行扩展的最佳实践。 

若要详细了解 ontology 的结构和建模约定、如何使用它、如何扩展它以及如何参与，请访问 GitHub 上的 ontology 存储库： [Azure/opendigitaltwins](https://github.com/Azure/opendigitaltwins-building)。 

你还可以在此博客文章中了解有关此计划的合作关系的详细信息和目标的详细信息，请参阅此博客文章中的 RealEstateCore 和目标： [RealEstateCore，这是数字孪生的智能建筑 ontology](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)。

## <a name="smart-cities-ontology"></a>智能城市 ontology

在 *此处找到 ontology：[**数字孪生定义语言 (DTDL) Ontology For Smart 市市市市市市市**](https://github.com/Azure/opendigitaltwins-smartcities)*。

Microsoft 与 [开放式 Agile 智能城市 (OASC) ](https://oascities.org/) 和 [Sirus](https://sirus.be/) ，为智能城市提供基于 DTDL 的 ONTOLOGY，从 [ETSI CIM NGSI](https://www.etsi.org/committee/cim)开始。 除了 ETSI NGSI-LD 外，我们还对 Saref4City、CityGML、ISO 和其他内容进行了评估。

当前版本的 ontology 侧重于一组初始模型。 Ontology 作者欢迎您参与扩展初始用例集，以及改进现有模型。 

若要了解有关 ontology、如何使用它以及如何参与的详细信息，请访问 GitHub 上的 ontology 存储库： [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities)。 

你还可以在此博客文章中阅读有关智能城市的合作关系和方法的详细信息，以及随附视频： [智能城市 Ontology 的数字孪生](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何扩展行业标准本体论以满足你的规范： [*概念：扩展行业本体论*](concepts-ontologies-extend.md)。

* 或者，继续查看基于本体论开发模型的路径： [*在模型开发路径中使用 ontology 策略*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)。