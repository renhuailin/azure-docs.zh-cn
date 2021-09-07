---
title: 选择 Azure Percept DK 更新包
description: 如何标识 Azure Percept DK 版本并选择最适合的更新包
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 3e18d8a009736e8e597d5accffb654c1b95aec5d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225212"
---
# <a name="select-your-azure-percept-dk-update-package"></a>选择 Azure Percept DK 更新包

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
1. 在“设备”中，选择你的开发工具包设备。
1. 在“常规”选项卡中，查找“模型”和“软件版本”信息。  

选项 2：

1. 从 Microsoft Azure 门户查看“IoT 中心”服务的 IoT Edge 设备。 
1. 从设备列表中选择开发工具包设备。
1. 选择“设备孪生”。
1. 滚动浏览设备孪生属性，在“deviceInformation”下面找到“model”和“swVersion”并记下其值。  

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
