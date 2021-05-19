---
title: 向现有 Azure Data Share 添加数据集
description: 了解如何向 Azure Data Share 中的现有数据共享添加数据集，以及如何与相同的收件人共享数据集。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910536"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>如何将数据集添加到 Azure Data Share 的现有共享中

本文介绍如何使用 Azure Data Share 向已存在的数据共享添加数据集。 通过此项操作可以与相同的收件人共享更多的数据，而不必创建新的共享。

有关如何在创建共享时添加数据集的信息，请参阅[共享数据](share-your-data.md)教程。

## <a name="navigate-to-a-sent-data-share"></a>导航到已发送的数据共享

在 Azure Data Share 中，导航到已发送的共享，然后选择“数据集”选项卡。单击“+ 添加数据集”按钮，添加更多数据集 。

![显示已选择“添加数据集”的屏幕截图。](./media/how-to/how-to-add-datasets/add-datasets.png)

在右侧面板中，选择要添加的数据集类型，然后单击“下一步”。 选择要添加的数据的订阅和资源组。 使用下拉箭头，查找并选中要添加的数据旁边的复选框。

![屏幕截图显示“添加 Blob 存储”窗格，可在其中选择数据。](./media/how-to/how-to-add-datasets/add-datasets-side.png)

单击“添加数据集”后，这些数据集将添加到你的共享中。 注意：快照必须由用户触发，才能让其查看新的数据集。 如果已配置快照设置，则在下一个计划的快照完成后，用户将看到新的数据集。 如果未配置快照设置，则用户必须手动触发数据的完整副本或增量副本才能接收更新。 有关快照的详细信息，请参阅[快照](terminology.md)。

## <a name="next-steps"></a>后续步骤
了解有关如何[将收件人添加到现有数据共享](how-to-add-recipients.md)的细信息。