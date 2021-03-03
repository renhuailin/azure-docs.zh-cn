---
title: 查看 Web 作业的日志历史记录
description: 查看失败和成功作业的日志历史记录。
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744467"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>查看 Azure 门户中的 Web 作业历史记录

查看失败和成功作业的日志历史记录。

1. 选择要查看其历史记录 Web 作业，并选择“日志”按钮。

    ![“日志”按钮](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. 在“Web 作业详细信息”页中，选择一个时间以查看一个运行轮次的详细信息。

    ![Web 作业详细信息](./media/web-sites-create-web-jobs/webjobdetails.png)

1. 在“Web 作业运行详细信息”页中，选择“切换输出”查看日志内容的文本。 

    ![Web 作业运行详细信息](./media/web-sites-create-web-jobs/webjobrundetails.png)

    若要在单独的浏览器窗口中查看输出文本，请选择“下载”。 若要下载文本本身，请右键单击“下载”，并使用浏览器选项来保存文件内容。

1. 选择页面顶部的“Web 作业”痕迹导航链接转到 Web 作业列表。

    ![Web 作业痕迹导航](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![历史记录仪表板中的 Web 作业列表](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>后续步骤

* 使用 [Web 作业 SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 简化许多编程任务

* 了解如何 [通过 Visual Studio 开发和部署 Web 作业](webjobs-dotnet-deploy-vs.md)