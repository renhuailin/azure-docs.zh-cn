---
title: 管理模型和学习设置 - 个性化体验创建服务
description: 机器学习模型和学习设置可以导出到自己的源代码管理系统中进行备份。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1e1e5dd9c21b89aba4c905656dd79c7934f8b061
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831600"
---
# <a name="how-to-manage-model-and-learning-settings"></a>如何管理模型和学习设置

机器学习模型和学习设置可以导出到自己的源代码管理系统中进行备份。

## <a name="export-the-personalizer-model"></a>导出个性化体验创建服务模型

在“模型和学习设置”的“资源管理”部分，查看模型创建日期和上次更新日期，然后导出当前模型。 可以使用 Azure 门户或个性化体验创建服务 API 导出模型文件以进行存档。

![导出当前个性化体验创建服务模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除学习循环的数据

1. 在 Azure 门户中，找到你的个性化体验创建服务资源，在“模型和学习设置”页面上选择“清除数据”。
1. 为了清除所有数据，并将学习循环重置为原始状态，请选择所有 3 个复选框。

    ![在 Azure 门户中，清除个性化体验创建服务资源中的数据。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|目的|
    |--|--|
    |已记录的个性化设置和奖励数据。|这些日志记录数据用于脱机评估。 如果要重置资源，可清除数据。|
    |重置个性化体验创建服务模型。|此模型在每次重新训练时都会更改。 训练频率在“配置”页上的“上传模型频率”中指定。 |
    |将学习策略设置为默认值。|如果在脱机评估过程中更改了学习策略，则此设置会将其重置为原始学习策略。|

1. 选择“清除所选数据”以开始清除过程。 右上方导航的 Azure 通知中会报告状态。

## <a name="import-a-new-learning-policy"></a>导入新学习策略

[学习策略](concept-active-learning.md#understand-learning-policy-settings)设置决定模型训练的超参数。 执行[脱机评估](how-to-offline-evaluation.md)可查找新的学习策略。

1. 打开 [Azure 门户](https://portal.azure.com)，选择个性化体验创建服务资源。
1. 在“资源管理”部分选择“模型和学习设置” 。
1. 要导入学习设置，请选择你用上面指定的 JSON 格式创建的文件，然后选择“上传”按钮。

    等待学习策略上传成功的通知。

## <a name="export-a-learning-policy"></a>导出学习策略

1. 打开 [Azure 门户](https://portal.azure.com)，选择个性化体验创建服务资源。
1. 在“资源管理”部分选择“模型和学习设置” 。
1. 要导出学习策略，请选择“导出学习设置” 。 `json` 文件将会保存到本地计算机中。

## <a name="next-steps"></a>后续步骤

[使用脱机评估分析学习循环](how-to-offline-evaluation.md)
