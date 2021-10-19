---
title: include 文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491abcae4bf73d6b2dcd8172fcb1b69cb7b60d32
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807481"
---
## <a name="create-a-topic-using-the-azure-portal"></a>使用 Azure 门户创建主题
1. 在“服务总线命名空间”页面上，选择左侧菜单中的“主题”。
2. 在工具栏中选择“+ 主题”。 
4. 输入主题名称。 将其他选项保留默认值。
5. 选择“创建”。

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-topic.png" alt-text="图像显示了“创建主题”页。":::

## <a name="create-a-subscription-to-the-topic"></a>创建主题的订阅
1. 选择在上一部分创建的主题。 
    
    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/select-topic.png" alt-text="图像显示主题列表中选择的主题。":::
2. 在“服务总线主题”页面上，选择工具栏上的“+ 订阅” 。 

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png" alt-text="图像显示“添加订阅”按钮。":::    
3. 在“创建订阅”页上执行以下步骤：
    1. 对于订阅名称，输入“S1” 。
    1. 对于“最大交付数”，输入“3” 。
    1. 然后，选择“创建”以创建订阅。 

        :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png" alt-text="图像显示了“创建订阅”页。":::
