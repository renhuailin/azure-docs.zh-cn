---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/11/2021
ms.openlocfilehash: 42af7691d7401e397e7e0d9819fded60b3821b26
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785294"
---
1. 使用 Azure 订阅的凭据登录到 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户的左上角，选择三个横杠，然后选择“+ 创建资源”。

    :::image type="content" source="media/aml-create-in-portal/create-resource.png" alt-text="屏幕截图显示了“+ 创建资源”。":::

1. 使用搜索栏查找“机器学习”。

1. 选择“机器学习”。

   :::image type="content" source="media/aml-create-in-portal/machine-learning.png" alt-text="屏幕截图显示了从搜索结果中选择“机器学习”。":::


1. 在“机器学习”窗格中，选择“创建”以开始 。

1. 提供以下信息来配置新工作区：

   字段|说明
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 本示例使用 docs-ws。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。
   订阅 |选择要使用的 Azure 订阅。
   资源组 | 使用订阅中的现有资源组，或者输入一个名称，创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 本示例使用 docs-aml。 
   位置 | 选择离你的用户和数据资源最近的位置来创建工作区。

1. 完成工作区配置后，选择“查看 + 创建”。
1. 选择“创建”以创建工作区。

   > [!Warning]
   > 在云中创建工作区可能需要几分钟时间。

   完成创建后，会显示部署成功消息。
 
 1. 若要查看新工作区，请选择“转到资源”。
 1. 从你的工作区的门户视图中，选择“启动工作室”以转到 Azure 机器学习工作室。 

