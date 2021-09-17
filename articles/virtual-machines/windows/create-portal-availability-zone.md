---
title: 使用 Azure 门户创建分区的 VM
description: 使用 Azure 门户在可用性区域中创建 VM
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 5/10/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 59250d3e2ed69da44e1d5e9187a12c1abac69d25
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688842"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>使用 Azure 门户在可用性区域中创建虚拟机

适用于：:heavy_check_mark: Windows VM 

本文逐步说明如何使用 Azure 门户在 Azure 可用性区域中创建虚拟机。 [可用性区域](../../availability-zones/az-overview.md)是 Azure 区域中物理上独立的区域。 使用可用性区域可以在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。

若要使用可用性区域，请在[受支持的 Azure 区域](../../availability-zones/az-region.md)中创建虚拟机。

## <a name="sign-in-to-azure"></a>登录 Azure 

1. 通过 https://portal.azure.com 登录到 Azure 门户。

1. 单击“创建资源” > “计算” > “虚拟机”  。 

3. 输入虚拟机信息。 用户名和密码用于登录到虚拟机。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](faq.yml#what-are-the-password-requirements-when-creating-a-vm-)。 

4. 选择一个区域，例如支持可用性区域的美国东部 2。 

5. 在“可用性选项”下，选择“可用性区域”下拉列表 。 

1. 在“可用性区域”下，从下拉列表中选择一个区域。
        
4. 为 VM 选择大小。 选择一个建议的大小，或根据功能进行筛选。 确认该大小在要使用的区域中可用。

6. 完成填写 VM 的信息。 完成操作后，选择“查看 + 创建”。

7. 验证信息后，选择“创建”。

1. 创建 VM 后，可以看到 VM 页面上的“Essentials”部分中列出的可用性区域。

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>为托管磁盘和 IP 地址确认区域

当 VM 部署在可用性区域中时，将在同一可用性区域中创建 VM 的托管磁盘。 默认情况下，还会在该区域中创建一个公共 IP 地址。

可以在门户中确认这些资源的区域设置。  

1. 从左侧菜单中选择“磁盘”，然后选择 OS 磁盘。 磁盘页面包含有关磁盘位置和可用性区域的详细信息。

1. 返回 VM 页面，选择公共 IP 地址。 在左侧菜单中，选择“属性”。 属性页面包含有关公共 IP 地址的位置和可用性区域的详细信息。

    
## <a name="next-steps"></a>后续步骤

本文介绍了如何在可用性区域中创建 VM。 详细了解 Azure VM 的[可用性](../availability.md)。
