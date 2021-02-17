---
title: 采用行业标准本体论
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生可采用的现有行业本体论
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561353"
---
# <a name="adopting-an-industry-ontology"></a>采用行业 ontology

由于使用开源 DTDL ontology （而不是从空白页开始）可以更轻松地开始，Microsoft 将与域专家合作发布本体论，这表示广泛接受的行业约定，并支持各种客户用例。 

结果是一组基于开源 DTDL 的本体论，可从中进行学习、构建、学习或直接使用行业标准。 本体论旨在满足下游开发人员的需求，并且可能由行业广泛采用和/或扩展。

目前，Microsoft 已与房地产合作伙伴合作，共同为智能建筑开发 ontology，这为基于行业标准的智能建筑建模提供了通用基础，以避免 reinvention。 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore 智能生成 ontology

Microsoft 与 [RealEstateCore](https://www.realestatecore.io/)（一座房地产物主、软件供应商和研究机构）合作，为房地产行业提供开源 DTDL ontology： [**针对智能建筑的 DTDL RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)。

此智能建筑 ontology 使用行业标准 (如程序 [块架构](https://brickschema.org/ontology/) 或 [W3C 构建拓扑 ontology](https://w3c-lbd-cg.github.io/bot/index.html)) 来模拟智能建筑物，以避免 reinvention。 Ontology 还附带了有关如何使用和正确地进行扩展的最佳实践。 

若要详细了解 ontology 的结构和建模约定、如何使用它、如何扩展它以及如何参与，请访问 [GitHub 上的 ontology 存储库](https://github.com/Azure/opendigitaltwins-building)。 

你还可以在此博客文章中了解有关此计划的合作关系的详细信息和目标的详细信息，请参阅此博客文章中的 RealEstateCore 和目标： [*RealEstateCore，这是数字孪生的智能建筑 ontology*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何扩展行业标准本体论以满足你的规范： [*概念：扩展行业本体论*](concepts-ontologies-extend.md)。

* 或者，继续查看基于本体论开发模型的路径： [*在模型开发路径中使用 ontology 策略*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)。