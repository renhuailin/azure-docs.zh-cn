---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 6b4ee09212bc62f43c583c73299ac33a842dc942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777827"
---
1. 选择所创建的 my-api1 应用程序（应用 ID：2）以打开其“概述”页。

1. 在“管理”下，选择“公开 API” 。
1. 选择“应用程序 ID URI”旁边的“设置”链接。  将默认值 (GUID) 替换为一个唯一名称（例如“tasks-api”，然后选择“保存”。 
 
   Web 应用在请求 Web API 的访问令牌时，应将此 URI 添加为你为 API 定义的每个范围的前缀。
1. 在“此 API 定义的范围”下选择“添加范围”。 
1. 若要创建一个用于定义对 API 的读取访问权限的范围，请输入以下值：

    a. 范围名称：tasks.read  
    b. 管理员同意显示名称：对任务 API 的读取访问权限  
    c. 管理员同意说明：允许对任务 API 进行读取访问

1. 选择“添加作用域”。

1. 选择“添加范围”，然后通过输入以下值来添加一个用于定义对 API 的写入访问权限的范围： 

    a. 范围名称：tasks.write  
    b. 管理员同意显示名称：对任务 API 的写入访问权限  
    c. 管理员同意说明：允许对任务 API 进行写入访问
1. 选择“添加作用域”。