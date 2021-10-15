---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 2a1c7c65721483e851a7686c38736f0664b965ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367322"
---
如果打算向数据集中添加新文件，请使用增量刷新将这些新文件添加到项目。   启用增量刷新时，将根据标记完成率定期检查数据集，以将新图像添加到项目。   项目包含的文件达到最大数 500,000 时，新数据检查将停止。

若要将更多文件添加到项目中，请使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)上载到 Blob 存储中的相应文件夹。 

如果希望项目持续监视数据存储中的新数据，请选择“启用增量刷新”。 启用后，数据将每天一次拉取到你的项目中。 将新数据添加到数据存储后，需要等待一段时间才能看到它显示在项目中。  可在项目的“详细信息”选项卡的“增量刷新”部分中看到上次刷新数据的时间戳 。
如果不希望数据存储中的新文件添加到项目，请取消选中此框。