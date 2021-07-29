---
title: Azure SAP HANA（大型实例）的加入要求 | Microsoft Docs
description: 了解 Azure SAP HANA（大型实例）的加入要求。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 899fd32a60f4938f42dc03b5ac836ff809c5c61f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579269"
---
# <a name="onboarding-requirements"></a>加入要求

本文列出了运行 Azure SAP HANA 大型实例（也称 BareMetal 基础结构实例）的要求。

## <a name="microsoft-azure"></a>Microsoft Azure

- 一个可以链接到 Azure 上的 SAP HANA（大型实例）的 Azure 订阅。
- Microsoft 顶级支持合同。 有关与在 Azure 中运行 SAP 相关的特定信息，请参阅 [SAP 支持说明 #2015553 – Microsoft Azure 上的 SAP：支持先决条件](https://launchpad.support.sap.com/#/notes/2015553)。 使用包含至少 384 个 CPU 的 HANA 大型实例单位时，还需要扩大顶级支持协定范围，使之包括 Azure 快速响应。
- 了解通过 SAP 完成[大小调整练习](hana-sizing.md)后所需的 [HANA 大型实例 SKU](hana-available-skus.md)。

## <a name="network-connectivity"></a>网络连接

- 本地到 Azure 之间的 ExpressRoute：若要将本地数据中心连接到 Azure，请确保从 ISP 订购至少 1 Gbps 的连接。 HANA 大型实例与 Azure 之间的连接也使用 ExpressRoute 技术。 HANA 大型实例与 Azure 之间的此 ExpressRoute 连接包含在 HANA 大型实例的价格中。 该价格还包括此特定 ExpressRoute 线路的所有数据流入和流出费用。 因此，你不会有超出本地与 Azure 之间的 ExpressRoute 链接的其他费用。

## <a name="operating-system"></a>操作系统

- SUSE Linux Enterprise Server 12 for SAP Applications 的许可证。

   > [!NOTE] 
   > Microsoft 提供的操作系统未向 SUSE 注册。 它未连接到订阅管理工具实例。

- 在 Azure 中的 VM 上部署的 SUSE Linux 订阅管理工具。 此工具使得 Azure 上的 SAP HANA（大型实例）能够由 SUSE 进行注册并相应地进行更新。 （HANA 大型实例数据中心内没有 Internet 访问。） 
- 适用于 SAP HANA 的 Red Hat Enterprise Linux 6.7 或 7.x 的许可证。

   > [!NOTE]
   > Microsoft 提供的操作系统未向 Red Hat 注册。 它未连接到 Red Hat 订阅管理器实例。

- 在 Azure 中的 VM 上部署的 Red Hat 订阅管理器。 Red Hat 订阅管理器使得 Azure 上的 SAP HANA（大型实例）能够由 Red Hat 进行注册并相应地进行更新。 （Azure 大型实例模具上部署的租户内没有直接 Internet 访问。）
- SAP 要求拥有支持与 Linux 提供程序的协定。 此要求不清除 HANA 大型实例或这一事实的解决方案，在 Azure 中的运行的 Linux。 与不同的 Linux Azure 库映像，一些服务费未包含在 HANA 大型实例解决方案产品/服务。 你负责履行 Linux 分发服务器的支持合同方面的 SAP 要求。 
   - 对于 SUSE Linux，请查看 [SAP 说明 #1984787 - SUSE Linux Enterprise Server 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787)和 [SAP 说明 #1056161 - SUSE 优先支持 SAP 应用程序](https://launchpad.support.sap.com/#/notes/1056161)中的支持合同要求。
   - 对于 Red Hat Linux，需要具有正确的订阅级别，其中包括支持和服务（HANA 大型实例的操作系统的更新）。 Red Hat 建议使用 Red Hat Enterprise Linux 订阅实现 SAP 解决方案。 请参阅 https://access.redhat.com/solutions/3082481 。 

有关不同 Linux 版本的不同 SAP HANA 版本的支持矩阵，请参阅 [SAP 说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。

有关操作系统和 HLI 固件/驱动程序版本的兼容性矩阵，请参阅 [HLI 的 OS 升级](os-upgrade-hana-large-instance.md)。


> [!IMPORTANT] 
> 对于类型 II 设备，目前仅支持 SLES 12 SP2 OS 版本。 


## <a name="database"></a>数据库

- SAP HANA（平台版或企业版）的许可证和软件安装组件。

## <a name="applications"></a>应用程序

- 连接到 SAP HANA 和相关的 SAP 支持合同的任何 SAP 应用程序的许可证和软件安装组件。
- 与 Azure 上的 SAP HANA（大型实例）环境和相关的支持合同配合使用的任何非 SAP 应用程序的许可证和软件安装组件。

## <a name="skills"></a>技能

- 有关 Azure IaaS 及其组件的经验和知识。
- 有关在 Azure 中部署 SAP 工作负荷的经验和知识。
- 通过了 SAP HANA 安装技能认证的人员。
- 针对 SAP HANA 设计高可用性和灾难恢复解决方案的 SAP 架构技能。

## <a name="sap"></a>SAP

- 你应该是一位 SAP 客户，与 SAP 有支持协定
- 请咨询 SAP，了解各版本的 SAP HANA，以及大型纵向扩展硬件的最终配置（尤其是在针对 II 类 HANA 大型实例 SKU 进行实施的情况下）。

## <a name="next-steps"></a>后续步骤

了解如何使用 SAP HANA 数据分层和扩展节点。

> [!div class="nextstepaction"]
> [使用 SAP HANA 数据分层和扩展节点](hana-data-tiering-extension-nodes.md)
