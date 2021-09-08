---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 400e8de6b8d8481cccd6d2b3bcb25620f956f823
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423266"
---
为了在 Azure 文件共享中保护数据以防数据丢失或损坏，所有 Azure 文件共享都在写入每个文件时存储了该文件的多个副本。 可以根据工作负载的要求选择额外的冗余度。 Azure 文件存储目前支持以下数据冗余选项：

- **本地冗余存储 (LRS)** ：使用 LRS 时，每个文件在 Azure 存储群集中存储三次。 这可以防止由于硬件故障（例如磁盘驱动器损坏）而导致数据丢失。 但是，如果数据中心内发生火灾或洪水等灾难，使用 LRS 的存储帐户的所有副本可能会丢失或无法恢复。
- **区域冗余存储 (ZRS)** ：使用 ZRS 时，将存储每个文件的三个副本，但这些副本位于不同的 Azure 可用性区域的三个不同存储群集中，它们在物理上是隔离的。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 在将副本写入所有三个可用性区域的存储群集前，不允许将其写入存储。 
- **异地冗余存储 (GRS)** ：使用 GRS 时，有两个区域，即一个主要区域和一个次要区域。 文件在主要区域中的 Azure 存储群集中存储三次。 写入将异步复制到 Microsoft 定义的次要区域。 GRS 提供 6 个数据副本，它们分布在两个 Azure 区域之间。 如果发生重大灾难，例如由于自然灾害或其他类似事件而导致 Azure 区域永久性丢失，Microsoft 将执行故障转移，次要区域将成为主要区域，为所有操作提供服务。 由于主要区域和次要区域之间的复制是异步的，因此，在发生重大灾难时，尚未复制到次要区域的数据将会丢失。 还可以执行异地冗余存储帐户的手动故障转移。
- **异地区域冗余存储 (GZRS)** ：可将 GZRS 看做类似于 ZRS，但具有异地冗余。 使用 GZRS 时，文件在主要区域的三个不同存储群集中存储三次。 所有写入都将异步复制到 Microsoft 定义的次要区域。 GZRS 的故障转移过程的工作原理与 GRS 相同。

不超过 5-TiB 的标准 Azure 文件共享支持全部 4 种冗余类型。 大于 5-TiB 的标准文件共享仅支持 LRS 和 ZRS。 高级 Azure 文件共享仅支持 LRS 和 ZRS。

常规用途版本 2 (GPv2) 存储帐户提供了两个 Azure 文件存储不支持的额外冗余选项：可读取访问的异地冗余存储（通常称为 RA-GRS）和可读取访问的异地区域冗余存储（通常称为 RA-GZRS）。 可以在存储帐户中使用这些选项集预配 Azure 文件共享，但 Azure 文件存储不支持从次要区域读取。 部署到可读取访问的异地冗余存储帐户或异地区域冗余存储帐户中的 Azure 文件共享将分别以异地冗余存储或异地区域冗余存储方式计费。