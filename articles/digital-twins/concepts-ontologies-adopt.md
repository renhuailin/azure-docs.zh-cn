---
title: 采用行业标准 ontology
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生目前可采用的行业 ontology
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102124221"
---
# <a name="adopting-an-industry-ontology"></a>采用行业 ontology

由于使用开源 DTDL ontology 比从空白页开始更加轻松，Microsoft 将与域专家合作发布 ontology，后者代表广泛接受的行业约定，并支持各种客户用例。 

因此，我们得到了一系列基于开源 DTDL 的 ontology，它们借鉴、构建、借鉴或直接使用行业标准。 这些 ontology 旨在满足下游开发者的需求，可能会被行业广泛采用和/或扩展。

目前，Microsoft 已与合作伙伴共同开发[智能建筑](#realestatecore-smart-building-ontology)和[智慧城市](#smart-cities-ontology)的 ontology，它们为基于这些行业中的标准进行建模提供了共同的基础，以避免重复工作。 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore 智能建筑 ontology

*在此查找 ontology：[**适用于智能建筑的基于数字孪生定义语言的 RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)* 。

Microsoft 已与 [RealEstateCore](https://www.realestatecore.io/)（瑞典房地产公司、软件供应商和研究机构的联盟）合作，为房地产行业提供此开源 DTDL ontology。

该智能建筑 ontology 使用行业标准（如  [BRICK 架构](https://brickschema.org/ontology/)或  [W3C 构建拓扑 ontology](https://w3c-lbd-cg.github.io/bot/index.html)）为智能建筑建模提供了共同的基础，以避免重复工作。 该 ontology 还提供有关如何使用和正确进行扩展的最佳实践。 

若要详细了解该 ontology 的结构和建模约定，以及如何使用、扩展和参与，请访问该 ontology 位于 GitHub 上的存储库：[Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building)。 

此外，还可通过以下博客文章和随附视频了解与 RealEstateCore 合作的详情以及此计划的目标：[适用于数字孪生的智能建筑 ontology RealEstateCore 现已可用](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)。

## <a name="smart-cities-ontology"></a>智慧城市 ontology

*在此查找 ontology：[**适用于智慧城市的数字孪生定义语言 (DTDL) ontology**](https://github.com/Azure/opendigitaltwins-smartcities)* 。

Microsoft 已与 [Open Agile Smart Cities (OASC)](https://oascities.org/) 和 [Sirus](https://sirus.be/) 合作，从 [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim) 开始为智慧城市提供基于 DTDL 的 ontology。 除 ETSI NGSI-LD 之外，我们还对 Saref4City、CityGML、ISO 等进行了评估。

当前版本的 ontology 侧重于一组初始模型。 Ontology 作者欢迎您参与扩展初始用例集，以及改进现有模型。 

若要了解有关该 ontology 以及如何使用和参与的详细信息，请访问该 ontology 位于 GitHub 上的存储库：[Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities)。 

此外，还可以在以下博客文章和随附视频中了解有关合作和实现智慧城市方法的详细信息：[适用于数字孪生的智慧城市 Ontology](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585)。

## <a name="next-steps"></a>后续步骤

* 了解有关扩展行业标准本体以满足要求的详细信息：[概念：扩展行业本体](concepts-ontologies-extend.md)。

* 或者，继续开发基于本体的模型：[在模型开发路径中使用本体策略](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)。