---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: bb1a40b4f8b52181b34d89d40ae58505356e93bf
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326280"
---
若要检索自定义模型训练数据的 SAS URL，请在 Azure 门户中找到存储资源，然后选择“存储资源管理器”选项卡。导航到你的容器，右键单击，并选择“获取共享访问签名”。 请务必获取容器的 SAS，而不是存储帐户本身的。 确保选中“读取”、“写入”、“删除”和“列表”权限，然后单击“创建”    。 然后将“URL”部分中的值复制到临时位置。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。
