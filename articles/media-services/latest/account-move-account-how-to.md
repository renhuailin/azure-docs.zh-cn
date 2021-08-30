---
title: 管理 Azure 媒体服务 v3 帐户
description: 若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 本文介绍如何管理 Azure 媒体服务 v3 帐户。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: cbda31a3aaaf741424db174aa6a676858b27a23c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471205"
---
# <a name="manage-azure-media-services-v3-accounts"></a>管理 Azure 媒体服务 v3 帐户

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。 媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 有关详细信息，请参阅[存储帐户](storage-account-concept.md)。

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="media-services-account-names"></a>媒体服务帐户名称

媒体服务帐户名称必须由小写字母或数字构成（不含空格），长度介于 3 到 24 个字符之间。 媒体服务帐户名称在 Azure 位置中必须是唯一的。

删除媒体服务帐户后，帐户名称将保留一年。 在帐户删除后的一年内，帐户名称只能由包含原始帐户的订阅在同一 Azure 位置重复使用。

媒体服务帐户名称用于 DNS 名称，包括密钥交付、实时事件和流式处理终结点名称。 如果已将防火墙或代理配置为允许媒体服务 DNS 名称，请确保在删除媒体服务帐户后的一年内删除这些配置。

## <a name="moving-a-media-services-account-between-subscriptions"></a>在订阅之间移动媒体服务帐户

如果需要将媒体服务帐户移到新订阅，需要先将包含媒体服务帐户的整个资源组移到新订阅。 你必须移动所有附加资源：Azure 存储帐户、Azure CDN 配置文件等。有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 与 Azure 中的任何资源一样，资源组移动可能需要一些时间才能完成。

### <a name="considerations"></a>注意事项

* 在迁移到其他订阅之前，请创建帐户中所有数据的备份。
* 需要停止所有流式处理终结点和实时传送视频流资源。 在资源组移动期间，你的用户将无法访问你的内容。 

> [!IMPORTANT]
> 在移动成功完成之前，请勿启动流式处理终结点。

### <a name="troubleshoot"></a>故障排除

如果媒体服务帐户或关联的 Azure 存储帐户在资源组移动后变为“已断开连接”状态，请尝试轮换存储帐户密钥。 如果轮换存储帐户密钥不能解决媒体服务帐户的“已断开连接”状态，请通过媒体服务帐户中的“支持 + 疑难解答”菜单来提交新的支持请求。  

## <a name="next-steps"></a>后续步骤

[创建帐户](./account-create-how-to.md)
