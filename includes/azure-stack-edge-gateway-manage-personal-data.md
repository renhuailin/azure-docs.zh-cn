---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466373"
---
- **订单详细信息**。 创建订单后，用户的送货地址、电子邮件地址和联系信息将存储在 Azure 门户中。 保存的信息包括：
  - 联系人姓名
  - 电话号码
  - 电子邮件地址
  - 街道地址
  - 城市
  - 邮政编码
  - 状态
  - 国家（地区）/省区市/区域
  - 运输跟踪号

    订单详细信息已加密并存储在服务中。 在显式删除资源或订单之前，服务会保留这些信息。 从设备运送起到退回 Microsoft 之前，都将阻止删除该资源和相应的订单。

- **送货地址**。 下单后，Data Box 服务会向第三方承运商（如 UPS）提供送货地址。

- **共享用户**。 设备上的用户还可访问共享上的数据。 可查看可访问共享数据的用户列表。 删除共享时也会删除此列表。