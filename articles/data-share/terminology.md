---
title: Azure Data Share 术语
description: 了解用于描述 Azure Data Share 中使用的资源的通用术语（数据提供者、数据使用者、数据共享、共享订阅、快照、邀请、收件人。）
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a02624f4e5cf3ebbcd2f476372707f58c1d99f69
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664060"
---
# <a name="azure-data-share-concepts"></a>Azure Data Share 概念 

Azure Data Share 引入了一些与数据共享有关的新术语。 本文介绍在整个服务期间可能会看到的一些常用术语。 

## <a name="data-provider"></a>数据提供程序

数据提供者是与其使用者共享数据的组织。 通常，数据提供者可能是数据的所有者或挑选者。 数据提供者希望共享各种类型的数据。 数据提供者可能希望共享的数据包括原始数据，例如销售点或时序数据。 数据提供者可能还希望共享包含分析和见解的精选预处理数据。 

## <a name="data-consumer"></a>数据使用者 

数据使用者是接收数据提供者的数据的组织。 数据使用者可能希望结合共享数据与自己的数据以获取见解。 在某些情况下，数据使用者可能会接收处理过的数据。 

## <a name="data-share"></a>Data Share

数据共享是作为单个实体共享的一组数据集。 数据集可来自 Azure Data Share 支持的多个 Azure 数据源。 目前，Azure 数据共享[支持数据存储](supported-data-stores.md#supported-data-stores)。 

## <a name="share-subscription"></a>共享订阅 

在数据使用者接受来自数据提供者的数据共享邀请时，将创建共享订阅。 数据提供者可以导航到其 Azure Data Share 帐户中的“发送的共享”，然后选择“共享订阅”来查看活动的共享订阅 。

数据使用者可以导航到“接收的共享”并查看接收的共享的状态来检查是否拥有活动的共享订阅。 

## <a name="snapshot"></a>快照

数据使用者可以在接受数据共享邀请时创建快照。 数据使用者接受邀请以后，即可触发与其共享的数据的完整快照。 该快照是数据使用者生成快照时的数据副本。 

快照有两种类型：完整快照和增量快照。 完整快照包含数据共享内的所有数据。 增量快照包含上次触发快照后更新/添加的所有数据。 

## <a name="snapshot-settings-in-azure-data-share"></a>Azure Data Share 中的快照设置
 
数据提供者可以为数据共享启用快照设置。 此设置使数据使用者可以在发生增量更新时接收增量更新。 如果数据提供者希望其数据使用者接收对已共享数据的更新，则应启用此设置。 

如果数据提供者启用此设置，则可以选择重复间隔。 重复间隔可以是每小时或每天。 

数据使用者可以选择加入此快照计划以接收增量更新，其中包括首次生成新快照后更改过的所有数据。 

## <a name="invitation"></a>邀请

数据提供者可以邀请多个收件人加入数据共享。 可以通过将收件人添加到数据共享来实现这一目标。 创建数据共享后，还可以添加邀请。 

数据提供者可以删除已发送的邀请，即使该邀请尚未被接受。 如果数据提供者删除邀请且该邀请尚未被接受，则数据使用者将无法接受该邀请。 

一天最多可以重复发送五次邀请。 

## <a name="recipient"></a>Recipient

收件人是接收数据共享邀请的人。 通常，数据提供者会将收件人添加到其创建的数据共享。 邀请的收件人接受邀请后便成为数据使用者。  

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。
