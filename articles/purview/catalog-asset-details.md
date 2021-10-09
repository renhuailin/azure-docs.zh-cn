---
title: 如何查看、编辑和删除资产
description: 这篇操作指南介绍如何查看和编辑资产详细信息。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8004ef319efc08610f9c1a5de16b7c430d51d666
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209119"
---
# <a name="view-edit-and-delete-assets-in-purview-catalog"></a>查看、编辑和删除 Purview 目录中的资产

本文讨论如何查看资产及其相关详细信息， 还将介绍如何编辑和删除目录中的资产。

## <a name="prerequisites"></a>先决条件

- 设置数据源并将资产扫描到目录中。
- 或使用 Purview Atlas API 将资产引入目录。 

## <a name="viewing-asset-details"></a>查看资产详细信息

可通过以下任一方法发现 Purview 中的资产：
- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)

找到要查找的资产后，可查看、编辑或删除其所有详细信息，如以下各部分所述。

## <a name="asset-details-tabs-explained"></a>资产详细信息选项卡说明

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="资产详细信息选项卡":::

- **概述** -“概述”选项卡涵盖资产的基本详细信息，如说明、分类、层次结构和术语表术语。
- **属性** -“属性”选项卡涵盖有关资产的基本属性和高级属性。
- **架构** -“架构”选项卡中显示资产的架构，包括列名、数据类型、列级分类、术语和说明。
- **世系** - 此选项卡包含可用资产的世系图详细信息。
- **联系人** - 每个资产都可分配有一名所有者和专家，可从“联系人”选项卡查看和管理这些人员。
- **相关** - 可通过此选项卡导航到与当前正在查看的资产相关的资产。 

## <a name="asset-overview"></a>资产概述
资产详细信息的概述部分提供了资产的汇总视图。 下列各部分介绍了概述页面的不同部分。

### <a name="asset-hierarchy"></a>资产层次结构

可在“概述”选项卡中查看完整的资产层次结构。例如：如果导航到 SQL 表，则可查看该表所属的架构、数据库和服务器。

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="资产层次结构":::

### <a name="asset-classifications"></a>资产分类

资产分类标识要表示的数据类型，可手动应用或在扫描期间应用。 例如：国民身份证号码或护照号码就是受支持的分类。 （有关分类的完整列表，请参阅[受支持的分类页](supported-classifications.md)。）“概述”选项卡反映了已应用的资产级分类和列级分类，还可将其作为架构的一部分查看。

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="资产分类":::

### <a name="asset-description"></a>资产说明

可在“概述”部分查看有关资产的说明。 可通过[编辑资产](#editing-assets)来添加资产说明

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="资产说明":::

### <a name="asset-glossary-terms"></a>资产术语表术语

资产术语表术语是业务术语的托管词汇，可用于对整个环境中的资产进行分类和关联。 例如，“客户”、“购买者”、“成本中心”等术语，或者为用户提供数据上下文的任何术语。 有关详细信息，请参阅[业务术语表页](concept-business-glossary.md)。 可在概述部分查看资产的术语表术语，并且可通过[编辑资产](#editing-assets)对资产添加术语表术语。

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="资产术语表术语":::

## <a name="editing-assets"></a>编辑资产

可选择资产左上角的编辑图标来编辑资产。

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="资产编辑和删除按钮":::

在资产级别，可停留在编辑屏幕的“概述”选项卡来编辑或添加说明、分类或术语表术语。

可导航到编辑屏幕上的“架构”选项卡来更新列名、数据类型、列级分类、术语或资产说明。

可导航到编辑屏幕的“联系人”选项卡来更新资产的所有者和专家。 可在 Azure Active Directory 中按用户的全名、电子邮件或别名进行搜索。

### <a name="edit-behavior-explained"></a>编辑行为说明

如果进行资产级更新，例如向资产添加说明、资产级分类、术语表术语或联系人，则后续扫描将更新资产架构（扫描程序在后续扫描运行中检测到的新列和分类）。

如果进行列级更新，例如添加说明、列级分类、术语表术语或更新数据类型或列名，则后续扫描将不会更新资产架构（扫描程序在后续扫描运行中不会检测到新列和分类） 。

## <a name="deleting-assets"></a>删除资产

可选择资产名称下的删除图标来删除资产。

### <a name="delete-behavior-explained"></a>删除行为说明

使用删除按钮删除的任何资产都将永久删除。 但是，如果对资产从中引入到目录中的源运行完全扫描，则资产将重新引入，并且你可在 Purview 目录中发现它。

如果按计划（每周或每月）对源进行扫描，则已删除的资产不会重新引入目录，除非自上一次运行扫描后最终用户修改了该资产。   例如，如果从 Purview 中删除了 SQL 表，但在删除该表后，用户向 SQL 表添加了一个新列，则下次扫描时，资产将对其重新扫描并引入到目录中。

如果删除某资产，则仅删除该资产。 Purview 当前不支持级联删除。 例如，如果删除目录中的存储帐户资产，其中的容器、文件夹和文件不会被删除。 

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
