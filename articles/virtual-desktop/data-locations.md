---
title: Azure 虚拟桌面的数据位置 - Azure
description: Azure 虚拟桌面数据和元数据存储位置的简要概述。
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 06/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 709721139fa652798c5cc67b7f54e1b7452505a3
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273868"
---
# <a name="data-locations-for-azure-virtual-desktop"></a>Azure 虚拟桌面的数据位置

Azure 虚拟桌面目前可用于所有地理位置。 管理员可以在创建主机池虚拟机和相关服务（如文件服务器）时选择存储用户数据的位置。 请查看 [Azure 数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/#overview)详细了解 Azure 地理位置。

>[!NOTE]
>Microsoft 不会控制或限制你或你的用户访问你的用户和特定于应用程序的数据的区域。

>[!IMPORTANT]
>Azure 虚拟桌面将多种类型的信息（如主机池名称、应用组名称、工作区名称和用户主体名称）存储在数据中心。 创建任何服务对象时，客户必须输入需要创建对象的位置。 该对象的位置决定对象信息的存储位置。 客户会选择某个 Azure 区域，并将相关信息存储在关联的地理区域中。 如需查看所有 Azure 区域和相关地理区域的列表，请访问 [https://azure.microsoft.com/global-infrastructure/geographies/](https://azure.microsoft.com/global-infrastructure/geographies/)。

本文介绍 Azure 虚拟桌面服务存储哪些信息。 若要详细了解客户数据定义，请参阅 [Microsoft 如何为联机服务对数据进行分类](https://www.microsoft.com/trust-center/privacy/customer-data-definitions)。

## <a name="customer-input"></a>客户输入

要设置 Azure 虚拟桌面服务，客户必须创建主机池和其他服务对象。 在配置过程中，客户必须提供主机池名称、应用程序组名称等信息。 该信息被视为客户输入。 客户输入存储在与创建对象的区域关联的地理位置中。 对象在 Azure 资源管理器中的路径被视为组织信息，因此数据驻留不适用于它们。 有关 Azure 资源管理器路径的数据将存储在所选地理位置之外。

## <a name="customer-data"></a>客户数据

该服务不直接存储任何用户或应用程序相关信息，但会存储应用程序名称和用户主体名称等客户数据，因为这些数据是对象设置过程的一部分。 此信息存储在与客户创建对象的区域关联的地理位置中。

## <a name="diagnostic-data"></a>诊断数据

每当客户或用户与服务交互时，Azure 虚拟桌面都会收集服务生成的诊断数据。 此数据仅用于以聚合形式对服务运行状况进行故障排除、支持和检查。 例如，在会话主机端，当 VM 注册到服务时，我们会生成包含虚拟机 (VM) 名称、VM 所属的主机池等的信息。 此信息存储在与创建主机池的区域关联的地理位置中。 此外，当用户连接到服务并启动远程桌面时，我们会生成诊断信息，其中包括用户主体名称、客户端位置、客户端 IP 地址、用户连接到的主机池等。 此信息将发送到两个不同的位置：

- 存在服务基础结构（客户端跟踪、用户跟踪和诊断数据）且最靠近用户的位置。
- 主机池所在的位置。

## <a name="service-generated-data"></a>服务生成的数据

为了让 Azure 虚拟桌面保持可靠且可缩放，我们会聚合流量模式和使用情况，以检查基础结构控制平面的运行状况和性能。 例如，为了解如何在服务使用量增加时增加区域基础结构的容量，我们会处理服务使用情况日志数据。 然后，我们会查看高峰时段的日志，并决定要添加哪些数据中心以满足此容量要求。 我们会从存在服务基础结构的所有位置聚合此信息，然后将其发送到美国区域。 发送到美国区域的数据包括清理数据，但不包括客户数据。

我们目前支持将上述数据存储到下列位置：

- 美国 (US)（正式发布）
- 欧洲 (EU)（正式发布）
- 英国 (UK)（正式发布）
- 加拿大 (CA)（正式发布）

随着服务的增长，我们会支持更多的地区。 存储的信息静态加密，并且异地冗余镜像在该地理区域维护。 客户数据（如应用程序设置和用户数据）将储存在客户选择的位置，并且不受服务管理。

为了进行灾难恢复，上述数据是在 Azure 地理区域内复制的。
