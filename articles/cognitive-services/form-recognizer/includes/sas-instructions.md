---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505318"
---
若要检索自定义模型训练数据的 SAS URL，请在 Azure 门户中找到存储资源，然后选择“存储资源管理器”选项卡。导航到你的容器，右键单击，并选择“获取共享访问签名”。 请务必获取容器的 SAS，而不是存储帐户本身的。 确保选中“读取”和“列表”权限，然后单击“创建”。 然后将“URL”部分中的值复制到临时位置。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。
   > [!div class="mx-imgBorder"]
   > ![替换文字](../media/quickstarts/get-sas-url.png)