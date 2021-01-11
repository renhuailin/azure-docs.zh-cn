---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 99dcfa46280c6fc00b27fa43fd6079c4ac32bd3a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704668"
---
### <a name="delete-azure-resources"></a>删除 Azure 资源 

删除 Azure 资源和资源组的操作不可逆。 请确保不要意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，请只删除 IoT 中心资源本身，而不要删除资源组。

若要删除资源，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。

2. 选择包含 IoT Edge 测试资源的资源组的名称。 

3. 查看资源组中所包含资源的列表。 若要删除这一切，可以选择“删除资源组”。  如果只需删除部分内容，可以单击要单独删除的每个资源。 
