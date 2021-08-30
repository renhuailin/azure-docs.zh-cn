---
title: 通过 Azure 门户还原 Azure Database for MySQL 灵活服务器。
description: 本文介绍如何通过 Azure 门户在 Azure Database for MySQL 灵活服务器中执行还原操作
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 687d9386d330e5b09366e22ace8f7fd8666ee9d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779679"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>使用 Azure 门户的 Azure Database for MySQL 灵活服务器（预览版）的时间点还原

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍使用备份在灵活服务器中执行时间点恢复的分步过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 必须具备 Azure Database for MySQL 灵活服务器。

## <a name="restore-to-the-latest-restore-point"></a>还原到最新还原点

按照以下步骤使用最早的现有备份还原灵活服务器。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2. 单击左侧面板中的“概述”。

3. 在“概述”页上单击“还原”。

4. 随即将显示还原页面，其中包含一个可以在最新还原点和自定义还原点之间进行选择的选项。

5. 选择“最新还原点”。

6. 在“还原到新服务器”字段中提供新的服务器名称。

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="最早还原时间":::

7. 单击“确定”。

8. 随即显示还原操作已启动的通知。

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>使用还原功能将服务器从公共访问移动到专用访问

按照以下步骤使用最早的现有备份还原灵活服务器。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2. 在“概述”页上单击“还原”。

3. 随即将显示还原页面，其中包含一个可以在最早还原点和自定义还原点之间进行选择的选项。

4. 选择“最早的还原点”或“自定义还原点” 。

5. 在“还原到新服务器”字段中提供新的服务器名称。

6. 在“还原到新服务器”字段中提供新的服务器名称。

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="查看概述":::

7. 转到“网络”选项卡以配置网络设置。

8. 在“连接方法”中，选择“专用访问（VNet 集成）” 。 转到“虚拟网络”部分，可以选择已有的“虚拟网络”和委托给 Microsoft.DBforMySQL/flexibleServers 的“子网”，也可单击“创建虚拟网络”链接创建新的虚拟网络   。
    > [!Note]
    > 下拉列表中只列出了同一区域和订阅中的虚拟网络和子网。 </br>
    > 所选子网将委托给 Microsoft.DBforMySQL/flexibleServers。 这意味着只有 Azure Database for MySQL 灵活服务器才能使用该子网。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet 配置":::

9. 创建新的“专用 DNS 区域”或选择现有的“专用 DNS 区域”。
    > [!NOTE]
    > 专用 DNS 区域名称必须以 `mysql.database.azure.com` 结尾。 </br>
    > 如果看不到用于创建新的专用 DNS 区域的选项，请在“基本信息”选项卡上输入服务器名称。</br>
    > 将灵活服务器部署到虚拟网络和子网后，无法将它移动到公共访问（允许的 IP 地址）。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="DNS 配置":::
10. 选择“查看 + 创建”，查看你的灵活服务器配置。
11. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

12. 随即显示还原操作已启动的通知。

## <a name="perform-post-restore-tasks"></a>执行还原后任务

完成还原后，应执行以下任务，然后用户和应用程序才能重新运行：

- 如果需要使用新的服务器来替换原始服务器，请将客户端和客户端应用程序重定向到新服务器。
- 对于要进行连接的用户，请确保设置适当的 VNet 规则。 不会从源服务器复制这些规则。
- 确保设置适当的登录名和数据库级权限。
- 根据需要为新还原服务器配置警报。

## <a name="next-steps"></a>后续步骤

详细了解[业务连续性](concepts-business-continuity.md)
