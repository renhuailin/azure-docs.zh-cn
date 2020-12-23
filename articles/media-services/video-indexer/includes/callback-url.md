---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994481"
---
一个 URL，用于通知客户（使用 POST 请求）以下事件：

- 索引状态更改： 
    - 属性：    
    
        |名称|说明|
        |---|---|
        |id|视频 ID|
        |state|视频状态|  
    - 示例： https： \/ /test.com/notifyme?projectName=MyProject&id = 1234abcd&状态 = 已处理
- 在视频中标识的人：
  - 属性
    
      |名称|说明|
      |---|---|
      |id| 视频 ID|
      |faceId|出现在视频索引中的人脸 ID|
      |knownPersonId|在人脸模型中唯一的个人 ID|
      |personName|人名|
        
    - 示例： https： \/ /test.com/notifyme?projectName=MyProject&id = 1234abcd&faceid = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&personName = Inigo_Montoya 
