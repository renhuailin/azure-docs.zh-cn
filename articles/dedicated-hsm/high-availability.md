---
title: 高可用性 - Azure 专用 HSM | Microsoft Docs
description: 了解 Azure 专用 HSM 高可用性的基本注意事项。 本文包含一个示例。
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c46ccbda35936ed94079a21e3e9c72405875b961
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608861"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure 专用 HSM 高可用性

Azure 专用 HSM 由 Microsoft 具有高可用性的数据中心提供支持。 但是，任何具有高可用性的数据中心都容易出现本地化故障，并且在极端情况下会出现区域级故障。 Microsoft 将 HSM 设备部署到区域中的不同数据中心以确保预配多个设备不会导致这些设备共享单个机架。 通过使用“Thales HA 组”功能在区域中的数据中心之间将这些 HSM 进行配对，可实现更高级别的高可用性。 还可以在区域之间将设备配对以在进行灾难恢复的情况下解决区域性故障转移。 使用此多层高可用性配置时，会自动解决任何设备故障以使应用程序保持正常工作。 所有数据中心的现场还具有备用设备和组件，以便可以及时更换任何失败的设备。

## <a name="high-availability-example"></a>高可用性示例

“Thales Luna 7 HSM 管理指南”中提供了有关如何在软件级别配置 HSM 设备以实现高可用性的信息。 此文档在 [Thales HSM 页面](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)提供。

下图展示了一个具有高可用性的体系结构。 它在区域中使用了多个设备并且在每个单独的区域中使用了多个配对的设备。 此体系结构使用了四 HSM 设备的最低配置和虚拟网络组件。

![高可用性关系图](media/high-availability/high-availability.png)

## <a name="next-steps"></a>后续步骤

建议在进行设备预配和应用程序设计或部署之前，先深刻理解此服务的所有重要概念，例如高可用性和安全性。
更多概念级别的主题：

* [部署体系结构](deployment-architecture.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [可支持性](supportability.md)
* [Monitoring](monitoring.md)

有关配置 HSM 设备以实现高可用性的具体详细信息，请参考 [Thales 客户支持门户](https://supportportal.thalesgroup.com/csm)上的管理员指南并查看第 6 部分。
