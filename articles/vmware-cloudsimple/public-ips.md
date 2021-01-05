---
title: Azure VMware 解决方案（按 CloudSimple）-分配公共 IP 地址
description: 描述如何在私有云环境中为虚拟机分配公共 IP 地址
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bb46ad726cd3b99324e9bb96b998ed1b4da885de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899178"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>为私有云环境分配公共 IP 地址

打开 "网络" 页上的 "公共 ip" 选项卡，为私有云环境中的虚拟机分配公共 IP 地址。

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md) ，并在侧菜单中选择 " **网络** "。
2. 选择 " **公共 ip**"。
3. 单击 " **新建公共 IP**"。

    ![公共 Ip 页面](media/public-ips-page.png)

4. 输入一个名称以标识 IP 地址条目。
5. 保留默认位置。
6. 如果需要，请使用滑块更改空闲超时值。
7. 输入要为其分配公共 IP 地址的本地 IP 地址。
8. 输入关联的 DNS 名称。
9. 单击“提交”  。

![分配公共 Ip](media/network-public-ip-allocate.png)

开始分配公共 IP 地址的任务。 可以在 **活动 > 任务** "页上检查任务的状态。 分配完成后，"公共 Ip" 页面上会显示新条目。
