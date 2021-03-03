---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5ef61502d0b2fccc92ca606992e965b45764baa0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744463"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>创建手动触发的 Web 作业

1. 进入 [Azure 门户](https://portal.azure.com)。
1. 中转到你的 **应用服务** <abbr title="应用资源可以是 web 应用、API 应用或移动应用。">应用资源</abbr>.
1. 选择“Web 作业”。

    ![选择“Web 作业”](../media/web-sites-create-web-jobs/select-webjobs.png)

2. 在“Web 作业”页中，选择“添加”。 

   ![“Web 作业”页](../media/web-sites-create-web-jobs/wjblade.png)

3. 使用表中指定的“添加 Web 作业”设置。

    ![显示创建手动触发的 Web 作业所需设置的设置项的屏幕截图。](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | 设置      | 示例值   | 说明  |
    | ------------ | ----------------- | ------------ |
   | <abbr title="在应用服务应用中唯一的名称。 必须以字母或数字开头，并且不能包含和以外的特殊 `-` 字符 `_` 。">名称</abbr> | myTriggeredWebJob | 
    | <abbr title="一个 *.zip* 文件，其中包含可执行文件或脚本文件，以及运行程序或脚本所需的所有支持文件。">文件上传</abbr> | ConsoleApp.zip |
    | <abbr title="类型包括连续、触发。">类型</abbr> | 触发 | 
    | <abbr title="类型包括计划或手动">导致</a> | 手动 | |

4. 单击 **“确定”** 。 

   新的 Web 作业随即显示在“Web 作业”页上。

   ![Web 作业列表](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **若要运行手动 WebJob**，请在列表中右键单击其名称，然后单击 " **运行**"。
   
    ![运行 Web 作业](../media/web-sites-create-web-jobs/runondemand.png)

