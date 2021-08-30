---
title: 选择 Azure Percept DK 更新包
description: 如何标识 Azure Percept DK 版本并选择最适合的更新包
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e2256ba6ad1f2f125b21e7cb59ab74b44864bce6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070776"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>如何确定和下载适用于 OTA 和 USB 更新的最佳更新包

本页介绍如何选择最适合开发工具包的更新包以及更新包的下载位置。

有关如何更新设备的详细信息，请参阅以下文章：
- [以无线方式更新 Azure Percept DK](./how-to-update-over-the-air.md)
- [通过 USB 更新 Azure Percept DK](./how-to-update-via-usb.md)


## <a name="prerequisites"></a>先决条件

- 已经[设置并连接到 Azure Percept Studio 和 IoT 中心](./quickstart-percept-dk-set-up.md)的 [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270)。

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>标识开发工具包的模型名称和软件版本
要确保将正确的更新包应用于开发工具包，必须先确定当前运行的软件版本。

> [!WARNING]
> 应用不正确的更新包可能会导致开发工具包无法操作。 必须按照以下步骤操作，以确保应用正确的更新包。

选项 1：
1. 登录到 [Azure Percept Studio](./overview-azure-percept-studio.md)。
2. 在“设备”中，选择你的开发工具包设备。
3. 在“常规”选项卡中，查找“模型”和“软件版本”信息。  

选项 2：
1. 从 Microsoft Azure 门户查看 IoT 中心服务的 IoT 边缘设备。 
2. 从设备列表中选择开发工具包设备。
3. 选择“设备孪生”。
4. 滚动浏览设备孪生属性，在“deviceInformation”下面找到“model”和“swVersion”并记下其值。  

## <a name="determine-the-correct-update-package"></a>确定正确的更新包
使用上一节中确定的 model 和 swVersion ，检查下表以确定要下载哪个更新包。


|模型  |swVersion  |Update 方法  |下载链接  |注意  |
|---------|---------|---------|---------|---------|
|PE-101     |所有的 swVersion       |仅 USB         |[2021.107.129.116 USB 更新包](https://go.microsoft.com/fwlink/?linkid=2169086)         |7 月版本 (2107)         |
|APDK-101     |任何早于 2021.106.111.115 的 swVersion |仅 USB         |[2021.107.129.116 USB 更新包](https://go.microsoft.com/fwlink/?linkid=2169086)         |7 月版本 (2107)         |
|APDK-101     |2021.106.111.115        |OTA 或 USB       |[2021.107.129.116 OTA 更新包](https://go.microsoft.com/fwlink/?linkid=2169245)<br>[2021.107.129.116 USB 更新包](https://go.microsoft.com/fwlink/?linkid=2169086)        |7 月版本 (2107)         |


## <a name="next-steps"></a>后续步骤
通过上一节中确定的方法和更新包来更新开发工具包。
- [以无线方式更新 Azure Percept DK](./how-to-update-over-the-air.md)
- [通过 USB 更新 Azure Percept DK](./how-to-update-via-usb.md)
