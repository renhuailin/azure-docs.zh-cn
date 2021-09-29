---
title: 创建映射数据流
description: 如何创建 Azure 数据工厂映射数据流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 83b40121de72dc45582ded94580154f5124817b0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061003"
---
# <a name="create-azure-data-factory-data-flow"></a>创建 Azure 数据工厂数据流

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

在 ADF 中映射数据流提供了一种无需任何编码即可大规模转换数据的方法，。 通过构造一系列转换，可以在数据流设计器中设计数据转换作业。 从任意数量的源转换开始，然后是数据转换步骤。 然后，使用接收器完成数据流，以将结果放在目标提供程序中。

首先从 Azure 门户创建一个新的 V2 数据工厂。 创建新工厂后，在“打开 Azure 数据工厂工作室”磁贴中选择“打开”，以启动数据工厂 UI。

:::image type="content" source="media/data-flow/v2portal.png" alt-text="屏幕截图显示了“版本”选择为 V2 的“新建数据工厂”窗格。":::

进入数据工厂 UI 后，你可以使用示例数据流。 示例可从 ADF 模板库中获得。 在 ADF 中，在主页的“发现更多”部分中选择“管道模板”磁贴，并从模板库中选择“数据流”类别。

:::image type="content" source="media/data-flow/template.png" alt-text="屏幕截图显示了选中“使用数据流转换数据”的“数据流”选项卡。":::

系统将提示输入 Azure Blob 存储帐户信息。

:::image type="content" source="media/data-flow/template2.png" alt-text="屏幕截图显示了“使用数据流转换数据”窗格，你可以在其中输入“用户输入”。":::

[可在此处找到用于这些示例的数据](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 下载示例数据并将文件存储在 Azure Blob 存储帐户中，以便执行示例。

## <a name="create-new-data-flow"></a>创建新的数据流

使用 ADF UI 中的“创建资源”“加号”按钮创建数据流。

:::image type="content" source="media/data-flow/newresource.png" alt-text="屏幕截图显示了从“工厂资源”菜单中选择的“数据流”。":::

## <a name="next-steps"></a>后续步骤

开始使用[源转换](data-flow-source.md)生成数据转换。
