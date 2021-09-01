---
title: 按国家/地区限制 Azure CDN 内容
description: 了解如何使用地区筛选功能按国家/地区来限制对 Azure CDN 内容的访问。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 07/07/2021
ms.author: allensu
ms.openlocfilehash: 58282d9880f1140def0e941bb8fb3d17356544d3
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221591"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>按国家/地区限制 Azure CDN 内容

## <a name="overview"></a>概述
当某个用户请求你的内容时，该内容会向所有位置的用户提供。 可能需要按国家/地区来限制对内容的访问。 

使用“地区筛选”功能，可以针对 CDN 终结点上的特定路径创建规则。 可以设置规则在所选国家/地区中允许或阻止内容。

> [!IMPORTANT]
> **Microsoft 的 Azure CDN 标准版** 配置文件不支持基于路径的地区筛选。
> 

## <a name="standard-profiles"></a>标准版配置文件

这些说明适用于 Akamai 的 Azure CDN Standard 配置文件和 Verizon 的 Azure CDN Standard 配置文件 。

对于 **来自 Verizon 的高级 Azure CDN** 配置文件，必须使用 **管理** 门户来激活地区筛选。 有关详细信息，请参阅[来自 Verizon 的高级 Azure CDN 配置文件](#azure-cdn-premium-from-verizon-profiles)。

### <a name="define-the-directory-path"></a>定义目录路径
若要访问地区筛选功能，请在门户中选择你的 CDN 终结点，然后在左侧菜单中的“设置”下选择“地区筛选”。 

![屏幕截图，显示从终结点的菜单中选择了“地区筛选”。](./media/cdn-filtering/cdn-geo-filtering-standard.png)

从“路径”框中，指定将允许或拒绝用户访问的位置的相对路径。 

可使用正斜杠 (/) 对所有文件应用地区筛选，也可通过指定目录路径（例如 */pictures/*）对选定文件夹应用该筛选。 也可以向单个文件应用地区筛选（例如 */pictures/city.png*）。 允许多个规则。 在输入一个规则后，会出现一个空白行供你输入下一个规则。

例如，以下所有目录路径筛选器都是有效的：   
*/*                                 
*/Photos/*      
*/Photos/Strasbourg/*      
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>定义操作的类型

从“操作”列表中，选择 **“允许”** 或 **“阻止”**： 

- 允许：只允许来自指定国家/地区的用户访问从该递归路径请求的资产。

- 阻止：拒绝来自指定国家/地区的用户访问从该递归路径请求的资产。 如果没有为该位置配置其他国家/地区筛选选项，将会允许所有其他用户进行访问。

例如，用于阻止路径 */Photos/Strasbourg/* 的地区筛选规则将筛选以下文件：     
http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg 

### <a name="define-the-countriesregions"></a>定义国家/地区

从“国家/地区代码”列表中，针对该路径选择要阻止或允许的国家/地区。 

在选择了国家/地区后，选择“保存”，以激活这个新地区筛选规则。 

![屏幕截图：显示用于阻止或允许国家或地区的国家/地区代码。](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>清理资源

若要删除某个规则，请从“地区筛选”页面上的列表中选择它，然后选择“删除”。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>来自 Verizon 的高级 Azure CDN 配置文件

对于 Verizon 的 Azure CDN Premium 配置文件，用于创建地区筛选规则的用户界面有所不同：

1. 从 Azure CDN 配置文件的顶部菜单中，选择“管理”。

2. 从 Verizon 门户中，选择“HTTP 大型”，然后选择“国家/地区筛选”。

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="屏幕截图显示如何在 Azure CDN 中选择国家/地区筛选" border="true":::
  
3. 选择“添加国家/地区筛选器”。

4. 在“第一步:”中，输入目录路径。 选择“阻止”或“添加”，然后选择“下一步”  。

    > [!IMPORTANT]
    > 终结点名称必须位于路径中。  示例：/myendpoint8675/myfolder。  请将“myendpoint8675”替换为终结点的名称。
    > 
    
5. 在“第二步:”中，从列表中选择一个或多个国家/地区。 选择“完成”，以激活该规则。 
    
    新规则将显示在“国家/地区筛选”页面上的表中。
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="屏幕截图显示规则在国家/地区筛选中出现的位置。" border="true":::
 
### <a name="clean-up-resources"></a>清理资源
在国家/地区筛选规则表中，选择某个规则旁边的删除图标可删除该规则，选择编辑图标可修改该规则。

## <a name="considerations"></a>注意事项
* 对地区筛选配置的更改不会立即生效：
   * 对于 **Microsoft 推出的 Azure CDN 标准版** 配置文件，传播通常可在 10 分钟内完成。 
   * 对于 **Akamai 的 Azure CDN 标准版** 配置文件，传播通常可在一分钟内完成。 
   * 对于“Verizon 提供的 Azure CDN 标准版”  和“Verizon 提供的 Azure CDN 高级版”  配置文件，传播通常在 10 分钟内完成。 
 
* 此功能不支持通配符（例如 *）。

* 与相对路径关联的地区筛选配置以递归方式应用于该路径。

* 只能对相同相对路径应用一个规则。 也就是说，无法创建多个指向同一相对路径的国家/地区筛选器。 不过，因为国家/地区筛选器是递归的，所以一个文件夹可以有多个国家/地区筛选器。 换言之，可以向某个以前配置的文件夹的子文件夹分配其他国家/地区筛选器。

* 地区筛选功能使用国家/地区代码来定义相关国家/地区，这样系统就可以允许或阻止这些国家/地区发出的针对受保护目录的请求。  Verizon 的 Azure CDN 和 Akamai 的 Azure CDN 配置文件使用 ISO 3166-1 alpha-2 国家/地区代码来定义允许或阻止安全目录请求的国家/地区。 

