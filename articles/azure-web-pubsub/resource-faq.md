---
title: Azure Web PubSub 服务常见问题解答
description: 获取有关 Azure Web PubSub 服务的常见问题的解答。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e53823539194fe1082621f458e8b5b3d493c191d
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166851"
---
# <a name="azure-web-pubsub-service-faq"></a>Azure Web PubSub 服务常见问题解答

这是 Azure Web PubSub 服务的常见问题解答。 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Azure Web PubSub 服务是已可用于生产？

Azure Web PubSub 服务现为公共预览版，没有承诺的 SLA。 

##  <a name="where-does-my-data-reside"></a>我的数据存放在什么位置？

Azure Web PubSub 服务以数据处理器服务的形式运行。 它不会存储任何客户内容，且设计包含数据驻留。 如果将 Azure Web PubSub 服务与其他 Azure 服务（例如 Azure 存储）结合来进行诊断，请查看[此白皮书](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)，获取有关如何将数据驻留在 Azure 区域中的指南。