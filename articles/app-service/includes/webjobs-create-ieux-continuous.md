---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744464"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>创建连续 Web 作业

1. 进入 [Azure 门户](https://portal.azure.com)。
1. 中转到你的 **应用服务** <abbr title="应用资源可以是 web 应用、API 应用或移动应用。">应用资源</abbr>.
1. 选择“Web 作业”。

   ![选择“Web 作业”](../media/web-sites-create-web-jobs/select-webjobs.png)

1. 在“Web 作业”页中，选择“添加”。 

    ![“Web 作业”页](../media/web-sites-create-web-jobs/wjblade.png)

1. 使用表中指定的“添加 Web 作业”设置。

   ![显示需要配置的“添加 Web 作业”设置的屏幕截图。](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 设置      | 示例值   | 
   | ------------ | ----------------- | 
   | <abbr title="在应用服务应用中唯一的名称。 必须以字母或数字开头，并且不能包含和以外的特殊 `-` 字符 `_` 。">名称</abbr> | myContinuousWebJob | 
   | <abbr title=" 一个 *.zip* 文件，其中包含可执行文件或脚本文件，以及运行程序或脚本所需的所有支持文件。">文件上传</abbr> | ConsoleApp.zip |
   | <abbr title="类型包括连续、触发。">类型</abbr> | 连续 | 
   | <abbr title="仅适用于连续 Web 作业。 确定程序或脚本是在所有实例还是只在一个实例上运行。 指定要在多个实例上运行的选项不适用于免费或共享定价层。">缩放</abbr> | 多实例 | 

1. 单击 **“确定”** 。

    新的 Web 作业随即显示在“Web 作业”页上。

    ![Web 作业列表](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **若要停止或重新启动** 连续 webjob，请右键单击列表中的 webjob，然后单击 " **停止** " 或 " **启动**"。

   ![停止连续 Web 作业](../media/web-sites-create-web-jobs/continuousstop.png)
