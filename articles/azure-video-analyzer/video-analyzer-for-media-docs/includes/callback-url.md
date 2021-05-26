---
author: Juliako
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: db2f6fe6175b8ff4d6371cb7074fb8c83af51c14
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384979"
---
一个 URL，用于通知客户（使用 POST 请求）以下事件：

- 索引状态更改： 
    - 属性：    
    
        |名称|说明|
        |---|---|
        |id|视频 ID|
        |state|视频状态|  
    - 示例：https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- 在视频中标识的人：
  - 属性
    
      |名称|说明|
      |---|---|
      |id| 视频 ID|
      |faceId|出现在视频索引中的人脸 ID|
      |knownPersonId|在人脸模型中唯一的个人 ID|
      |personName|人名|
        
    - 示例：https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 
