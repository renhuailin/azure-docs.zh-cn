---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: b61e9ba2374286a313444de7ab1e74b17c0a7af5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382213"
---
### <a name="upgrade-personalizer-instance-to-multislot"></a>将个性化体验创建服务实例升级到多槽

在 Azure 门户的“模型和学习设置”页上的个性化体验创建服务资源中，单击“导出学习设置”。 下载的 json 文件中的“arguments”字段将以“--cb_explore_adf”开头。 将其更改为“--ccb_explore_adf”，同时保持文件的其余部分不变。 保存该文件。 

![更改前的学习设置](../media/settings/learning-settings-pre-upgrade.png)

![更改后的学习设置](../media/settings/learning-settings-post-upgrade.png)

在门户的同一选项卡的“导入学习设置”下浏览，以查找最近修改的 json 文件并将其上传。 这会将个性化体验创建服务实例更新为“多槽”个性化体验创建服务，现在将支持多槽排名和奖励调用。