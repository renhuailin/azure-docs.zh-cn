---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553134"
---
1. 登录到 Data Box 设备。 请确保该设备已解锁。

    ![屏幕截图显示设备显示为“已解锁”的仪表板。](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. 转到“设置网络接口”  。 记下用来连接到客户端的网络接口的设备 IP 地址。

    ![屏幕截图显示可在其中查看 I P 地址的网络设置。](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. 转到“连接和复制”  ，并单击“Rest”  。

    ![屏幕截图显示可在其中选择 REST 作为访问设置的“连接和复制”窗格。](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. 从“访问存储帐户和上传数据”  对话框中，复制 **Blob 服务终结点**。

    ![屏幕截图显示可在其中复制 Blob 服务中终结点的“访问存储帐户和上传数据”对话框。](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. 以管理员身份启动 **记事本**，并打开位于 `C:\Windows\System32\Drivers\etc` 的 **hosts** 文件。
6. 将以下条目添加到 **hosts** 文件中：`<device IP address> <Blob service endpoint>`
7. 请使用以下图像作为参考。 保存 **hosts** 文件。

    ![屏幕截图显示添加了 IP 地址和 blob 服务终结点的记事本文档。](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
