---
title: 使用 Purview Studio
description: 此概念性文章介绍了如何使用 Azure Purview Studio。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: ca25bbb72ff853f819f3e8ce4e0092ddb762b156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183805"
---
# <a name="use-purview-studio"></a>使用 Purview Studio

本文概述了 Azure Purview 的一些主要功能。

## <a name="prerequisites"></a>先决条件

* 用户已在 Azure 门户中创建活动的 Active Purview 帐户，并且该用户有权访问 Purview Studio。

## <a name="launch-purview-account"></a>启动 Purview 帐户

* 若要启动 Purview 帐户，请在 Azure 门户中转到 Purview 帐户，选择要启动的帐户，然后启动该帐户。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="选择启动 Azure Purview 帐户目录的屏幕截图。":::

* 启动 Purview 帐户的另一种方法是：转到 `https://web.purview.azure.com`，选择 Azure Active Directory 和帐户名称，以启动帐户。

## <a name="home-page"></a>主页

“主页”是 Azure Purview 客户端的起始页。

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="主页的屏幕截图。":::

下面的列表汇总了主页上的主要功能。 列表中的每个编号都与前面屏幕截图中某个突出显示的编号相对应。

1. 易记的目录名称。 可以在“管理中心” > “帐户信息”中设置目录名称。

2. 目录分析显示以下项的数量：
    - 用户、组和应用程序
    - 数据源
    - 资产
    - 术语表术语

3. 通过搜索框，可以在数据目录中搜索数据集。

4. 通过快速访问按钮，可以访问常用的应用程序功能。 所显示的按钮取决于分配给用户帐户的角色。

    - 为“数据策展人”角色显示的按钮是“知识中心”、“浏览资产”、“管理术语表”和“查看见解”   。
    - 为“数据读者”角色显示的精选按钮是“知识中心”、“浏览资产”、“查看术语表”和“查看见解”   。
    - 为“数据源管理员 + 数据策展人”角色显示的精选按钮是“知识中心”、“注册数据源”、“浏览资产”和“管理术语表”。    
    - 为“数据源管理员 + 数据读者”角色显示的精选按钮是“知识中心”、“注册数据源”、“浏览资产”和“查看术语表”。    

5. 左侧导航栏可帮助你定位到应用程序的主页面。 所显示的按钮取决于分配给用户帐户的角色。

    - 为“数据策展人”角色显示的按钮是“主页”、“术语表”、“见解”和“管理中心”。   
    - 为“数据读者”角色显示的按钮是“主页”、“术语表”、“见解”和“管理中心”。   
    - 为“数据源管理员 + 数据策展人/读者”角色显示的按钮是“主页”、“源”、“术语表”、“见解”和“管理中心”。     
  
6. “最近访问的内容”选项卡显示最近访问过的数据资产的列表。 有关访问资产的信息，请参阅[在数据目录中搜索](how-to-search-catalog.md)和[按资产类型浏览](how-to-browse-catalog.md#browse-experience)。  “我的项目”选项卡列出了已登录用户拥有的数据资产。
7. “实用链接”包含指向区域状态、文档、定价、概述和 Purview 状态的链接
8. 顶部导航栏包含有关发行说明/更新、更改 Purview 帐户、通知、帮助和反馈部分的信息。

## <a name="knowledge-center"></a>知识中心

可以在“知识中心”中找到所有与 Purview 相关的视频和教程。

## <a name="guided-tours"></a>引导式演示

Azure Purview Studio 中的每个 UX 都提供用于简要介绍相应页面的引导式演示。 若要启动引导式演示，请在顶部栏中选择“帮助”，然后选择“引导式演示” 。

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="引导式演示的屏幕截图。":::

> [!Important]
   > “数据源管理员”角色本身不具有对 Purview Studio 的访问权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加安全主体](tutorial-scan-data.md)
