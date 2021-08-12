---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: cd6bddb6988555b336cb920c481fe675e9f7a276
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072961"
---
1. 选择你创建的 my-api1 应用程序以打开其“概述”页面。
1. 在“管理”下，选择“公开 API” 。
1. 选择“应用程序 ID URI”旁边的“设置”链接。  将默认值 (GUID) 替换为一个唯一的名称，例如 `tasks-api`，然后选择“保存”。 Web 应用在请求 Web API 的访问令牌时，应将此 URI 添加为你为 API 定义的每个范围的前缀。
1. 在“此 API 定义的范围”下选择“添加范围”。 
1. 输入以下值来创建一个定义对 API 的读取访问权限的范围，然后选择“添加范围”：
    1. **范围名称**：`tasks.read`
    1. **管理员许可显示名称**：`Read access to tasks API`
    1. **管理员许可说明**：`Allows read access to the tasks API`
1. 选择“添加范围”，输入以下值来添加一个定义对 API 的写入访问权限的范围，然后选择“添加范围”： 
    1. **范围名称**：`tasks.write`
    1. **管理员许可显示名称**：`Write access to tasks API`
    1. **管理员许可说明**：`Allows write access to the tasks API`