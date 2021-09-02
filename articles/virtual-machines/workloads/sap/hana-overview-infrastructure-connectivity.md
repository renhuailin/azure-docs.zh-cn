---
title: Azure 上的 SAP HANA（大型实例）的基础结构和连接 | Microsoft Docs
description: 配置所需的连接基础结构，以使用 Azure 上的 SAP HANA（大型实例）。
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
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8fc161f83bcdb070ca4be1c1da3be84f1086c93
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412540"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA（大型实例）部署 

本文将列出部署 SAP HANA 大型实例（另称为 BareMetal 基础结构实例）所需的信息。 首先，有关背景信息，请参阅：
- [HANA 大型实例常见术语](hana-know-terms.md)
-  [HANA 大型实例 SKU](hana-available-skus.md)

## <a name="required-information"></a>必需的信息

你已从 Microsoft 购买 Azure SAP HANA 大型实例并想要部署。 Microsoft 需要你提供以下信息：

- 客户名称。
- 业务联系人信息（包括电子邮件地址和电话号码）。
- 技术联系人信息（包括电子邮件地址和电话号码）。
- 技术网络联系人信息（包括电子邮件地址和电话号码）。
- Azure 部署区域（例如“美国西部”、“澳大利亚东部”或“北欧”）。
- Azure 上的 SAP HANA 配置（大型实例）SKU（配置）。
- 对于每个 Azure 部署区域：
    - 用于将 Azure 虚拟网络连接到 HANA 大型实例的 ER-P2P 连接的 /29 IP 地址范围。
    - 用于 HANA 大型实例服务器 IP 池的 /24 CIDR 块。
    - 使用 [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) 时可选，并请保留另一个 /29 IP 地址范围。 添加的范围允许从本地直接路由到 HANA 大型实例单位。 添加的范围还支持在不同 Azure 区域的 HANA 大型实例单元之间路由。 此特定范围不能与之前定义的任何其他 IP 地址范围重叠。
- 对于连接到 HANA 大型实例的每个 Azure 虚拟网络，虚拟网络地址空间属性中使用的 IP 地址范围值。
- 适用于每个 HANA 大型实例系统的数据：
  - 所需主机名，最好是使用完全限定的域名。
  - 超出服务器 IP 池地址范围的 HANA 大型实例单元的相应 IP 地址。 （服务器 IP 池地址范围中的前 30 个 IP 地址被保留，以供 HANA 大型实例内部使用。）
  - SAP HANA 实例的 SAP HANA SID 名称（创建必要的 SAP HANA 相关磁盘卷时需要用到）。 Microsoft 需要使用 HANA SID 在 NFS 卷上创建 sidadm 权限。 这些卷附加到 HANA 大型实例单元。 HANA SID 还用作已装载磁盘卷的名称组件之一。 若要在单元上运行多个 HANA 实例，应该列出多个 HANA SID。 每个都会分配有一组单独的卷。
  - 在 Linux OS 中，sidadm 用户具有一个组 ID。 需要使用此 ID 创建 SAP HANA 相关的所需磁盘卷。 SAP HANA 安装通常使用组 ID 1001 创建 sapsys 组。 sidadm 用户属于该组。
  - 在 Linux OS 中，sidadm 用户具有一个用户 ID。 需要使用此 ID 创建 SAP HANA 相关的所需磁盘卷。 如果在单元上运行多个 HANA 实例，请列出所有 sidadm 用户。 
- Azure 上的 SAP HANA 大型实例将直接连接到的 Azure 订阅的 Azure 订阅 ID。 此订阅 ID 指代将按一个或多个 HANA 大型实例单元收费的 Azure 订阅。

提供上述信息后，Microsoft 将预配 Azure 上的 SAP HANA（大型实例）。 Microsoft 会发送所需的信息用于将 Azure 虚拟网络链接到 HANA 大型实例。 你还可以访问 HANA 大型实例单元。

## <a name="next-steps"></a>后续步骤

请按顺序参阅以下文章，并在 Microsoft 部署 HANA 大型实例后与这些实例建立连接：

1. [将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)
2. [将 VNet 连接到 HANA 大型实例 ExpressRoute](hana-connect-vnet-express-route.md)
3. [其他网络要求（可选）](hana-additional-network-requirements.md)
