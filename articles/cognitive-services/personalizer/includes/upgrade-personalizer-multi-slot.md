---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: 0f181a28d1522fb433a274361c96e532c6a0b708
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112254997"
---
### <a name="upgrade-personalizer-instance-to-multi-slot"></a>将个性化体验创建服务实例升级到多槽

> [!NOTE]
> 多槽个性化设置（预览版）会影响个性化体验创建服务的其他功能。 无法撤消此更改。 在启用多槽个性化设置之前，请参阅[多槽个性化设置（预览版）](../concept-multi-slot-personalization.md)。 


1. 如要在 Azure 门户中禁用自动优化，请在个性化体验创建服务资源中，在“资源管理”下的“模型和学习设置”页上，将“自动优化”关闭并保存。

> [!NOTE]
> 只有禁用自动优化，多槽个性化设置才有效。 未来将支持多槽个性化设置的自动优化。 

2. 如要在 Azure 门户中将个性化体验创建服务升级到多槽，请在个性化体验创建服务资源中，在“资源管理”下的“模型和学习设置”页上，单击“导出学习设置”。 下载的 json 文件中的“arguments”字段将以“--cb_explore_adf”开头。 将此项更改为 --ccb_explore_adf 并保存文件。 CB（上下文 Bandits）和 CCB（条件上下文 Bandits）是个性化体验创建服务分别用于单槽和多槽个性化设置的算法。 ADF（操作依赖功能）意味着操作由功能来表示/识别。

![更改前的学习设置](../media/settings/learning-settings-pre-upgrade.png)

![更改后的学习设置](../media/settings/learning-settings-post-upgrade.png)

在门户的同一选项卡的“导入学习设置”下浏览，以查找最近修改的 json 文件并将其上传。 这会将个性化体验创建服务实例更新为“多槽”个性化体验创建服务，现在将支持多槽排名和奖励调用。


