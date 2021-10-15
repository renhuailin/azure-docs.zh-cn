---
title: 在 Azure 市场中查找租户 ID、对象 ID 和合作伙伴关联详细信息
description: 如何在 Azure 市场中查找订阅 ID 的租户 ID、对象 ID 和合作伙伴关联详细信息。
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 92819c28002c79ff905174e5b2f94779050237fd
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356719"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>查找租户 ID、对象 ID 和合作伙伴关联详细信息

本文介绍如何查找租户 ID、对象 ID 和合作伙伴关联详细信息以及各自的订阅 ID。

如果需要获取 Azure Cloud Shell 中这些项的屏幕截图以帮助进行调试，请跳转到[查找用于调试的租户、对象和合作伙伴 ID 关联](#find-ids-for-debugging)。

>[!Note]
> 只有订阅的所有者才有权执行这些步骤。

## <a name="find-tenant-id"></a>查找租户 ID

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 选择“Azure Active Directory”  。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure 门户中的“Azure Active Directory”图标。":::

3. 选择“概述”。  租户 ID 应显示在“基本信息”下。

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="在 Azure 门户中选择“组”。":::

## <a name="find-subscriptions-and-roles"></a>查找订阅和角色

1. 转到 Azure 门户，然后选择上面的步骤 1 和 2 中所述的“Azure Active Directory”。
2. 选择“订阅”。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure 门户中的“订阅”图标。":::

3. 查看订阅和角色。

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure 门户中的“订阅”屏幕。":::

## <a name="find-partner-id"></a>查找合作伙伴 ID

1. 按照上一部分中所述导航到“订阅”页面。
2. 选择一个订阅。
3. 在“计费”下，选择“合作伙伴信息” 。

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="左侧导航菜单中的“合作伙伴信息”。":::

## <a name="find-user-object-id"></a>查找用户（对象 ID）

1. 在所需租户中，使用具有相应管理权限的帐户登录到 [Office 365 管理门户](https://portal.office.com/adminportal/home)。
2. 在左侧菜单中，展开底部的“管理中心”部分，然后选择“Azure Active Directory”选项，在新的浏览器窗口中启动管理控制台。
3. 选择“用户”。
4. 浏览到或搜索所需的用户，然后选择帐户名称以查看用户帐户的个人资料信息。
5. 对象 ID 位于右侧的“标识”部分。

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory 管理中心。":::

6. 通过选择左侧菜单中的“访问控制(IAM)”然后选择“角色分配”，查找“角色分配”  。

    :::image type="content" source="../role-based-access-control/media/role-assignments-portal/rg-role-assignments.png" alt-text="Azure 资源的“角色分配”。":::


## <a name="find-ids-for-debugging"></a>查找用于调试的 ID

本部分介绍如何查找租户、对象和合作伙伴 ID 关联，以用于调试目的。

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 通过选择右上角的 PowerShell 图标打开 Azure Cloud Shell。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="屏幕右上角的 PowerShell 图标。":::

3. 从列表中选择“PowerShell”。

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="选择 PowerShell 链接。":::

4. 选中“订阅”框以选择合作伙伴链接到的订阅，然后选择“创建存储” 。 这是一次性操作；如果已设置存储，请继续执行下一步。

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="选择“创建存储”按钮。":::

5. 创建（或已拥有）存储会打开 Azure Cloud Shell 窗口。

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure Cloud Shell 窗口。":::

6. 如需获取合作伙伴关联详细信息，请通过以下命令安装扩展：<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell 将说明是否已安装扩展：

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Azure Cloud Shell 窗口展示已安装扩展。":::

7. 使用以下命令查看合作伙伴详细信息：<br>`az managementpartner show --partner-id xxxxxx`<br>示例：`az managementpartner show --partner-id 4760962`。<br>对齐命令结果的屏幕截图，其外观类似于：

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="示例屏幕显示前一个命令的结果，用于查看合作伙伴 ID。":::

>[!NOTE]
>如果多个订阅需要屏幕截图，请使用此命令在各订阅之间切换：<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>后续步骤

- [支持的国家和地区](sell-from-countries.md)