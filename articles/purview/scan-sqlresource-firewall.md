---
title: 扫描防火墙后面的 Azure SQL 数据库
description: 了解如何使用 Azure 监控范围门户扫描防火墙后面的资源。
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551677"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>在 Azure 监控范围中扫描防火墙后面的 Azure SQL 数据库

本文介绍如何使用 Azure 监控范围扫描防火墙后面的资源。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你自己的 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="set-up-sql-storage-behind-a-firewall"></a>设置防火墙后面的 SQL 存储

第一步是通过 [注册和扫描 AZURE Sql 数据库](register-scan-azure-sql-database.md)，将目录的 MSI 添加到 AZURE sql DB。

## <a name="scan-sql-db-from-purview"></a>从监控范围扫描 SQL DB

1. 浏览到监控范围主页。

1. 在左侧导航栏中选择 " **管理中心** "。

1. 选择 "源"**和 "扫描**" 下的 **数据源**。

1. 选择 " **+ 新建** " 以添加数据源。

1. 选择“Azure SQL 数据库”。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="选择 sql server 的屏幕截图。":::

1. 为新数据源提供名称，选择 " **从 Azure 订阅**"，然后从下拉列表中选择订阅和服务器名称。

   选择 " **完成** " 以完成注册。 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="完成选择的屏幕截图":::

1. 选择 "为防火墙后面的存储 **设置扫描** 并测试连接"。 连接指示它已成功。 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="选择 &quot;t0 设置扫描&quot; 的屏幕截图。":::

1. 设置扫描频率，然后选择 " **继续**"。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="启动 sql 扫描所选内容的屏幕截图。":::

1.  扫描完成后，状态会在数据源列表中更新。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="消息扫描已完成的屏幕截图":::
   
## <a name="next-steps"></a>后续步骤

接下来，您可以设置扫描规则集 [创建扫描规则集](create-a-scan-rule-set.md) ，以便将扫描组合在一起。
