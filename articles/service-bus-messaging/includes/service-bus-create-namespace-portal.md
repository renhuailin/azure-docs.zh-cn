---
title: 包含文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 978e703285317c7a644f6d0c459a1f1d572b9179
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129801126"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>在 Azure 门户中创建命名空间
若要开始在 Azure 中使用服务总线消息实体，必须先使用在 Azure 中唯一的名称创建一个命名空间。 命名空间提供了用于对应用程序中的 Service Bus 资源进行寻址的范围容器。

创建命名空间：

1. 登录到 [Azure 门户](https://portal.azure.com)
2. 在门户的左侧导航窗格中，依次选择“+ 创建资源”、“集成”、“服务总线”    。

    :::image type="content" source="./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png" alt-text="图像显示“创建资源”、“集成”以及菜单中的“服务总线”选择。":::
3. 在“创建命名空间”页的“基本信息”标记中，执行以下步骤 ： 
    1. 对于“订阅”，请选择要在其中创建命名空间的 Azure 订阅。 
    1. 对于“资源组”，请选择该命名空间驻留到的现有资源组，或创建一个新资源组。       
    1. 输入 **命名空间的名称**。 系统会立即检查该名称是否可用。 若需用于对命名空间进行命名的规则的列表，请参阅[创建命名空间 REST API](/rest/api/servicebus/create-namespace)。
    1. 对于“位置”，请选择托管该命名空间的区域。 
    1. 对于“定价层”，请选择命名空间的定价层（“基本”、“标准”或“高级”）。 对于本快速入门，请选择“标准”。 
    
        若要使用[主题和订阅](../service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)，请选择“标准”或“高级”。 基本定价层不支持主题/订阅。 

        如果选择了“高级”定价层，请指定“消息传送单元”数 。 高级层在 CPU 和内存级别提供资源隔离，使每个工作负荷在隔离的环境中运行。 此资源容器称为消息传送单元。 高级命名空间至少具有一个消息传送单元。 可为每个服务总线高级命名空间选择 1、2 或 4 个消息传送单元。 有关详细信息，请参阅[服务总线高级消息传送](../service-bus-premium-messaging.md)。
    1. 选择“查看 + 创建”  。 系统现已创建命名空间并已将其启用。 可能需要等待几分钟，因为系统将为帐户配置资源。
   
        :::image type="content" source="./media/service-bus-create-namespace-portal/create-namespace.png" alt-text="图像显示“创建命名空间”页":::
    1. 在“查看 + 创建”页上，查看设置，然后选择“创建” 。 
4. 在“部署”页上选择“转到资源”。 

    :::image type="content" source="./media/service-bus-create-namespace-portal/deployment-alert.png" alt-text="图像显示“部署成功”页，其中包括“转到资源”链接。":::
6. 将会看到服务总线命名空间的主页。 

    :::image type="content" source="./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png" alt-text="图像显示已创建的服务总线命名空间的主页。" :::

## <a name="get-the-connection-string"></a>获取连接字符串 
创建新的命名空间会自动生成一个初始共享访问签名 (SAS) 策略，该策略包含主密钥和辅助密钥以及主要连接字符串和辅助连接字符串，每个连接字符串都授予对命名空间所有方面的完全控制权。 请参阅[服务总线身份验证和授权](../service-bus-authentication-and-authorization.md)，了解如何创建规则来对普通发送者和接收者的权限进行更多限制。 

若要复制命名空间的主要连接字符串，请执行以下步骤： 

1. 在“服务总线命名空间”页中的左侧菜单上，选择“共享访问策略” 。
3. 在“共享访问策略”页，选择“RootManageSharedAccessKey” 。
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-info.png" alt-text="屏幕截图显示“共享访问策略”窗口，其中突出显示了策略。":::
4. 在“策略:  RootManageSharedAccessKey”窗口中，单击“主连接字符串”旁边的复制按钮，将连接字符串复制到剪贴板供稍后使用  。 将此值粘贴到记事本或其他某个临时位置。
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-string.png" alt-text="屏幕截图显示了名为 RootManageSharedAccessKey 的 S A S 策略，其中包含密钥和连接字符串。":::

    可使用此页面复制主密钥、辅助密钥和辅助连接字符串。 
