---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807801"
---
若要检索自定义模型训练数据的 SAS URL，请在 Azure 门户中找到存储资源，然后选择“存储资源管理器”选项卡。导航到你的容器，右键单击，并选择“获取共享访问签名”。 请务必获取容器的 SAS，而不是存储帐户本身的。 确保选中“读取”和“列表”权限，然后单击“创建”。 然后将“URL”部分中的值复制到临时位置。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。