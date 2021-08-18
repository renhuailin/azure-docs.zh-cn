---
title: Azure Data Share 的安全性概述
description: Azure Data Share 的安全性概述
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 770ee01d054aec084e9abd609af28e8125844e0f
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895861"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share 的安全性概述

本文介绍 Azure Data Share 服务的安全性概述。

## <a name="security-overview"></a>安全概述

Azure Data Share 利用 Azure 提供的基础安全措施来保护静态数据和传输中的数据。 将会对静态数据加密，通过基础数据存储提供支持。 也会使用 TLS 1.2 对传输中的数据加密。 此外还会对静态的和传输中的数据共享元数据加密。 Azure Data Share 不存储正在共享的客户数据的内容。

Azure Data Share 利用托管标识（之前称为 MSI）来访问用于数据共享的数据存储。 在数据提供者和数据使用者之间没有凭据交换。 有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。 有关共享数据所需的角色和权限的详细信息，请参阅[角色和要求](concepts-roles-permissions.md)。

## <a name="access-control"></a>访问控制

可以在 Data Share 资源级别设置对 Azure Data Share 的访问控制，确保它由那些获得授权的用户访问。 数据共享资源的所有者和参与者可以共享数据、接收共享和对现有共享进行更改。 数据共享资源的读者可以查看共享，但无法进行更改。 

创建或接收共享后，对数据共享资源拥有适当权限的用户可以进行更改。 当创建或接收共享的用户离开组织时，它不会终止共享或停止数据流。 对数据共享资源具有适当权限的其他用户可以继续管理该共享。

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>启用防火墙时，从数据存储共享数据或将数据共享到数据存储
若要在打开防火墙的情况下从存储帐户共享数据或将数据共享到存储帐户，需要在存储帐户中启用“允许受信任的 Microsoft 服务”。 请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md#trusted-microsoft-services)了解详细信息。


## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。
