---
title: 了解 Azure Purview 中的业务术语表功能
description: 本文介绍 Azure Purview 中的业务术语表。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: b332a00f511ea747c2978dafbf60ce20abca36a8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214401"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>了解 Azure Purview 中的业务术语表功能

本文概述了 Azure Purview 中的业务术语表功能。 

## <a name="business-glossary"></a>业务术语表

术语表为业务用户提供词汇。  术语表包含可彼此关联的业务术语，并允许对它们进行分类，以便可以在不同的上下文中理解这些业务术语。 然后，可以将这些术语映射到数据库、表、列等资产。这有助于抽象化与数据存储库相关的技术术语，并让业务用户能够发现和处理他们更熟悉的词汇表中的数据。

业务术语表是词汇的集合。 每个术语表示组织中的一个对象，并且很有可能多个术语表示同一对象。 客户也可以称为顾客、买方或买家。 这几个术语彼此相关。 这些术语之间的关系可以是以下其中一种：

- 同义词 - 定义相同，但术语不同
- 相关 - 定义类似，但名称不同

同一术语也可能表示多个业务对象。 务必明确定义每个术语的含义，并在组织内清晰地了解每个术语的含义。

## <a name="custom-attributes"></a>自定义特性

对于任何业务术语表术语，Azure Purview 支持 8 个现成的属性：
- 名称
- 定义
- 数据专员
- 数据专家
- 首字母缩写词
- 同义词
- 相关术语
- 资源

无法编辑或删除这些属性。 但是，这些属性不足以完全定义组织中的术语。 为了解决此问题，Purview 提供了一项为术语表定义自定义属性的功能。

## <a name="term-templates"></a>术语模板

术语模板提供术语表自定义属性，这些属性将在目录中以逻辑方式分组在一起。 此功能可以将所有相关的自定义属性组合到一个模板中，然后可在创建术语表术语时应用该模板。 例如，所有与财务相关的自定义属性（如成本中心、利润中心、会计代码）都可以分组到术语模板“财务模板”中，而财务模板则可用于创建财务术语表术语。

所有标准属性都按系统默认模板分组。 创建的任何术语模板将包含这些属性以及在创建模板过程中创建的任何其他自定义属性。

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>词汇表、分类、敏感度标签之间的比较

虽然术语表术语、分类和标签都是对数据资产的批注，但每一项在目录上下文中具有不同的含义。 

### <a name="glossary"></a>术语表

如上所述，业务术语表术语为组织定义业务词汇，有助于消除公司中各部门之间的隔阂。

### <a name="classifications"></a>分类

分类是可以分配给实体的注释。 由于分类的灵活性，你可以将分类用于多种方案，例如：

- 了解数据资产中存储的数据的性质
- 定义访问控制策略

目前，Purview 具有 200 多个系统分类器，用户可以在目录中定义自己的分类器。 在扫描过程中，我们会自动检测这些分类，并将它们应用于数据资产和架构。 不过，可以在任何时间点替代这些分类。 自动扫描绝对无法取代人工替代。

### <a name="sensitivity-labels"></a>敏感度标签

敏感度标签是一种批注类型，用于对组织的数据进行分类和保护，而不会影响工作效率和协作。 敏感度标签用于标识组织数据中的分类类型类别，以及要应用于每个类别的策略组。 Purview 使用与 Microsoft 365 相同的敏感信息类型，支持将现有的安全策略和保护延伸到整个内容和数据资产。 在 Purview 中，可以在 Microsoft Office 产品和数据资产之间共享相同标签。

## <a name="next-steps"></a>后续步骤

- [管理术语模板](how-to-manage-term-templates.md)
- [在 Azure Purview 中浏览数据目录](how-to-browse-catalog.md)
