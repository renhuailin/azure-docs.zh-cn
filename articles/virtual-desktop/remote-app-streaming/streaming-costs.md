---
title: 估计 Azure 虚拟桌面每用户应用流式处理成本 - Azure
description: 如何估计 Azure 虚拟桌面每用户计费成本。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d0218031a79e5271dcdff7531f856094175c477
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798722"
---
# <a name="estimate-per-user-app-streaming-costs-for-azure-virtual-desktop"></a>估计 Azure 虚拟桌面每用户应用流式处理成本

通过 Azure 虚拟桌面每用户访问定价，你可以向组织外部的用户授予对 Azure 虚拟桌面托管的应用和桌面的访问权限。 若要详细了解 Azure 虚拟桌面每用户访问定价，请参阅[了解授权和每用户访问定价](licensing.md)。 本文介绍如何估计部署的用户访问成本（假定为全价）。

>[!NOTE]
>本文中的价格基于每用户订阅全价，不含促销套餐或折扣。 另外请记住，Azure 虚拟桌面部署会产生其他费用，例如基础结构使用成本。 若要详细了解其他这些成本，请参阅[了解 Azure 虚拟桌面总部署成本](total-costs.md)。

## <a name="requirements"></a>要求

在估计现有部署的每用户访问成本之前，需要具备以下各项：

- 上个月拥有活跃用户的 Azure 虚拟桌面部署。
- [适用于 Azure 虚拟桌面部署的 Azure Monitor](../azure-monitor.md)

## <a name="measure-monthly-user-activity-in-a-host-pool"></a>度量主机池中的每月用户活动

为了估计运行主机池的总成本，首先需要了解过去一个月内活跃用户的数量。 可以使用 Azure 虚拟桌面的 Azure Monitor 来查找此数量。

若要在 Azure Monitor 上查看每月活跃用户数，请执行以下操作：

1. 打开 Azure 门户，搜索并选择“Azure 虚拟桌面”。 然后选择“见解”，打开适用于 Azure 虚拟桌面的 Azure Monitor。

2. 选择要度量的订阅或主机池的名称。

3. 在“概述”选项卡的“利用率”部分中，查找“每月用户数 (MAU)”图表 。

4. 查找最新日期的每月活跃用户数 (MAU) 的值。 MAU 显示在该日期之前的 28 天内连接到此主机池的用户数。

## <a name="estimate-per-user-access-costs-for-an-azure-virtual-desktop-host-pool"></a>估计 Azure 虚拟桌面主机池的每用户访问成本

接下来，我们查找每计费周期计费的金额。 此数量由至少连接到已注册订阅中的一个会话主机的用户数决定。

此外，有两个用户价格层：

- 仅连接到 RemoteApp 应用程序组的用户。
- 至少连接到一个桌面应用程序组的用户。

通过查看每个定价层中连接到订阅中的会话主机的用户数，可以估计总成本。

若要查看每层中的用户数，请执行以下操作：

1. 转到 [Azure 虚拟桌面定价页](https://azure.microsoft.com/pricing/details/virtual-desktop/) 并查找你区域的“应用”和“应用 + 桌面”价格。
2. 使用在步骤 4 [度量主机池中的每月用户活动](#measure-monthly-user-activity-in-a-host-pool)中找到的连接用户数来计算用户访问总成本。
   
   如果主机池使用 RemoteApp 应用程序组，则需要将连接的用户数乘以“应用”中显示的价格值。 换句话说，需要使用以下公式：

   连接的用户数 x 每用户的“应用”价格 = 总成本

   如果主机池使用桌面应用程序组，请需要将连接的用户数改乘以每用户的“应用 + 桌面”价格，如下所示：

   连接的用户数 x 每用户的“应用 + 桌面”价格 = 总成本

>[!IMPORTANT]
>根据你的环境，实际价格可能与以下说明中的估计值差别很大。 例如，估计值可能比实际成本高，因为用户访问多个主机池中的资源，但每个计费周期只按每用户收费一次。 如果你的数据所依据的 28 天时间窗口内的用户活动与典型的月度用户活动不一致，则估计值还可能低于成本。 例如，当月存在为期一周的长假或者发生过重大服务中断，导致用户活动低于平均水平，则估计值就会不准确。

## <a name="next-steps"></a>后续步骤

如果你有兴趣了解如何估计整个 Azure 虚拟桌面部署的总成本，请参阅[了解 Azure 虚拟桌面部署总成本](total-costs.md)。 若要了解有关授权要求和成本的信息，请参阅[了解授权和每用户访问定价](licensing.md)。
