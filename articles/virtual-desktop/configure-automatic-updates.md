---
title: 配置 Microsoft Endpoint Configuration Manager - Azure
description: 如何配置 Microsoft Endpoint Configuration Manager，以便将软件更新部署到 Azure 虚拟桌面上的 Windows 10 企业版多会话。
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: femila
ms.openlocfilehash: 8266473553288584a534e8833d8c50dcb2ec40cf
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754540"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>配置 Microsoft Endpoint Configuration Manager

本文介绍如何配置 Microsoft Endpoint Configuration Manager，以便自动将更新应用到运行 Windows 10 企业版多会话的 Azure 虚拟桌面主机。

## <a name="prerequisites"></a>先决条件

若要配置此设置，需执行以下操作：

   - 确保已在虚拟机上安装了 Microsoft Endpoint Configuration Manager Agent。
   - 确保 Microsoft Endpoint Configuration Manager 的版本至少为分支级别 1906。 为了获得最佳效果，请使用分支级别 1910 或更高版本。

## <a name="configure-the-software-update-point"></a>配置软件更新点

若要接收 Windows 10 企业版多会话的更新，需要启用作为 Microsoft Endpoint Configuration Manager 中的产品的 Windows Server 版本 1903 及更高版本。 如果使用 Windows Server Update Service 将更新部署到系统，则此产品设置同样适用。

若要接收更新：

1. 打开 Microsoft Endpoint Configuration Manager，然后选择“站点”。
2. 选择“配置站点组件”。
3. 在下拉菜单中选择“软件更新点”。
4. 选择 **产品** 选项卡。
5. 选中“Windows Server 版本 1903 及更高版本”复选框。
6. 转到“软件库” > “概述” > “软件更新” > “所有软件更新”，然后选择“同步软件更新”。    。
7. 在“程序文件” > “Microsoft Configuration Manager” > “日志”中检查 wsyncmgr.log 文件，以确保更改已保存。   同步更新可能需要几分钟。

## <a name="create-a-query-based-collection"></a>创建基于查询的集合

若要创建 Windows 10 企业版多会话虚拟机的集合，可以使用基于查询的集合来标识特定的操作系统 SKU。

创建集合：

1. 选择“资产和符合性”。
2. 转到“概述” > “设备集合”，然后右键单击“设备集合”，从下拉菜单中选择“创建设备集合”。   
3. 在打开的菜单的“常规”选项卡中，在“名称”字段中输入描述集合的名称。  在“注释”字段中，可以提供描述集合是什么的其他信息。 在“限制集合”中，定义要包含在集合查询中的计算机。
4. 在“成员资格规则”选项卡中，通过选择“添加规则”，然后选择“查询规则”，为查询添加规则。  
5. 在“查询规则属性”中，输入规则的名称，然后通过选择“编辑查询语句”来定义规则的参数。 
6. 选择“显示查询语句”。
7. 在此语句中，输入以下字符串：

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. 单击“确定”以创建集合。
9. 若要检查是否已成功创建集合，请转到“资产和符合性” > “概述” > “设备集合”。  
