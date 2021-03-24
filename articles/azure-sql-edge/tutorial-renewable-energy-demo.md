---
title: 在 Contoso 风力发电厂中的涡轮机上部署 Azure SQL Edge
description: 在本教程中，你将使用 Azure SQL Edge 对 Contoso 风力发电厂中的涡轮机进行尾流检测。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723467"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>使用 Azure SQL Edge 构建更智能的可再生资源

此 Azure SQL Edge 演示基于 Contoso Renewable Energy，这是一个风力涡轮发电场，使用 SQL DB edge 进行发电机上的数据处理。 

此演示将引导你解决由于在设备上检测到风湍流而引起的警报。 你将对模型进行训练，并将其部署到 SQL DB Edge，以更正检测到的风尾流并最终优化电能输出。

Azure SQL Edge - 第 9 频道上的可再生能源演示视频：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>在本地计算机上设置演示
Git 将用于将所有文件从演示复制到本地计算机。 

1. 从[此处](https://git-scm.com/download)安装 git。
2. 打开命令提示符，然后导航到应该将存储库下载到的文件夹。 
3. 发出命令 https://github.com/microsoft/sql-server-samples.git。
4. 导航到“sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo”（位于克隆存储库的位置）。
5. 请按照 README.md 中的说明设置演示环境并执行演示。