---
author: jianleishen
ms.service: data-factory
ms.topic: include
ms.date: 09/29/2021
ms.author: jianleishen
ms.openlocfilehash: a5fbf3071c134b52fb053afc88f6efb51a5ea2f8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725775"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
如果数据存储位于本地网络、Azure 虚拟网络或 Amazon Virtual Private Cloud 内部，则需要配置[自承载集成运行时](../create-self-hosted-integration-runtime.md)才能连接到该数据存储。

如果数据存储是托管的云数据服务，则可以使用 Azure Integration Runtime。 如果访问范围限制为防火墙规则中允许的 IP，你可以选择将 [Azure Integration Runtime IP](../azure-integration-runtime-ip-addresses.md) 添加到允许列表。 

此外，还可以使用 Azure 数据工厂中的[托管虚拟网络集成运行时](../tutorial-managed-virtual-network-on-premise-sql-server.md)功能访问本地网络，而无需安装和配置自承载集成运行时。

要详细了解网络安全机制和数据工厂支持的选项，请参阅[数据访问策略](../data-access-strategies.md)。
