---
title: 如何监视 Azure Data Share
description: 了解如何在 Azure Data Share 中监视邀请状态、共享订阅和快照历史记录
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910468"
---
# <a name="monitor-azure-data-share"></a>监视 Azure Data Share  

本文介绍如何使用 Azure Data Share Azure 来监视数据共享。 作为数据提供者，您可以监视数据共享关系的各个方面。 可以监视各种详细信息，例如，数据使用者是否接受了你对数据共享的邀请，以及他们是否已创建共享订阅并开始使用你的数据。 

作为数据使用者，你可以监视已触发到你的 Azure 订阅中的快照。 

## <a name="monitor-invitation-status"></a>监视邀请状态

通过导航到“已发送共享”->“邀请”查看数据共享邀请的状态。 

![邀请状态](./media/invitation-status.png "邀请状态") 

你的邀请可以处于三种状态之一：

* 挂起 - 数据共享接收方尚未接受邀请。
* 已接受 - 数据共享接收方已接受邀请。
* 已拒绝 - 数据共享收件人已拒绝邀请。

> [!IMPORTANT]
> 如果你在接受邀请后删除邀请，则不等同于撤消访问权限。 如果你想要阻止未来的快照复制到你的数据使用者存储帐户，则必须通过“共享订阅”选项卡撤销访问权限。 

## <a name="monitor-share-subscriptions"></a>监视共享订阅

通过导航到“已发送共享”->“邀请”来查看共享订阅的状态。 这将显示在接受你的邀请后数据使用者创建的活动订阅的相关详细信息。 通过选择共享订阅并选择“撤消”即可停止对数据使用者的将来更新。 

## <a name="snapshot-history"></a>快照历史记录 

在共享的“历史记录”选项卡中，可以查看将数据从数据提供者复制到数据使用者的数据存储的时间。 可以监视每个快照的频率、持续时间和状态。 

![在 Azure 门户中显示“已发送共享”的屏幕截图。](./media/sent-shares.png "快照历史记录") 

可以通过单击运行开始日期来查看有关每个快照运行的更多详细信息。 然后单击每个数据集的状态，以查看已传输的数据量、已复制的文件/记录的数量、快照的持续时间、使用的 Vcore 数和错误消息（如果有）。 

最多可显示 30 天的快照历史记录。 如果需要保存并查看 30 天以上的历史记录，可以利用诊断设置。

## <a name="diagnostic-setting"></a>诊断设置

可以配置诊断设置来保存日志数据或事件。 导航至“监视”->“诊断设置”，然后选择“添加诊断设置”。 选择感兴趣的日志数据或事件，以及要存储或发送的事件的位置。 

![在 Azure 门户中显示“诊断设置”页的屏幕截图。](./media/diagnostic-settings.png "诊断设置") 

## <a name="next-steps"></a>后续步骤 

详细了解 [Azure Data Share 术语](terminology.md)