---
title: 在 Contoso 风电场中的涡轮机上部署 Azure SQL Edge
description: 在本教程中，你将使用 Azure SQL Edge 对 Contoso 风电场中的涡轮机进行尾流检测。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723467"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>使用 Azure SQL Edge 构建更智能的可再生资源

此 Azure SQL Edge 演示基于 Contoso 可再生能源，一个使用 SQL DB Edge 在发电机上进行数据处理的风力涡轮机电场。 

此演示将引导你解决由于在设备上检测到风湍流而引发的警报。 你将对模型进行训练，并将其部署到 SQL DB Edge，以更正检测到的风尾流并最终优化电能输出。

Azure SQL Edge - 第 9 频道上的可再生能源演示视频：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>在本地计算机上设置演示
可使用 Git 将演示中的所有文件复制到本地计算机。 

1. 从[此处](https://git-scm.com/download)安装 git。
2. 打开命令提示符并导航到应将存储库下载到的文件夹。 
3. 发出命令 https://github.com/microsoft/sql-server-samples.git。
4. 在要将存储库克隆到的位置，导航到“sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo”。
5. 按照 README.md 中的说明设置演示环境，并执行演示。