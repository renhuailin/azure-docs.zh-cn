---
title: 了解 Azure 裸机基础结构的术语
description: 了解 Azure 裸机基础结构的术语。
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: a4bf8b24bc412215f595e12128c0a63f40e8fbfd
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767103"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>了解裸机基础结构的术语

本文介绍与 BareMetal 基础结构相关的一些重要术语。

- **修订**：BareMetal 基础结构（HANA 大型实例）缩放单元有两个不同的缩放单元修订。 这些修订在体系结构和接近 Azure 虚拟主机方面有所不同：
    - “修订 3”(Rev 3)：2016 年年中部署的原始设计。
    - “修订 4.2”(Rev 4.2)：一种新的设计，可提供与 Azure 虚拟机主机更接近的网络延迟，并大大降低 Azure VM 与 HANA 大型实例之间的网络延迟。 Azure 门户中的资源称为“BareMetal 基础结构"，客户可以从 Azure 门户中作为 BareMetal 实例访问其资源。

- **戳**：定义 BareMetal 实例的 Microsoft 内部部署大小。 必须先在数据中心位置部署一个由计算、网络和存储机架组成的 BareMetal 实例戳，才能部署实例。 此类部署称为 BareMetal 实例戳。

- **租户**：部署 BareMetal 实例戳的客户被隔离为租户。 租户在网络、存储和计算层中相互隔离。 分配给不同租户的存储和计算单位在 BareMetal 实例戳级别上不能相互可见或通信。 客户可以选择部署到不同的租户中。 即使这样，在 BareMetal 实例戳级别上租户之间也没有通信。

## <a name="next-steps"></a>后续步骤

详细了解 BareMetal 基础结构。

> [!div class="nextstepaction"]
> [BareMetal 基础结构](concepts-baremetal-infrastructure-overview.md)
