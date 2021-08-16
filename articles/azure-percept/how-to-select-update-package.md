---
title: 为 Azure Percept DK 选择最佳更新包
description: 如何标识 Azure Percept DK 版本并选择最适合的更新包
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 05/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 7528e11e24485fecc3e83773d98e119d98ca63cf
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968765"
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
|PE-101     |2020.108.101.105, <br>2020.108.114.120, <br>2020.109.101.122, <br>2020.109.116.120, <br>2021.101.106.118        |仅 USB         |[2021.105.111.112 USB 更新包](https://go.microsoft.com/fwlink/?linkid=2155734)         |5 月版本 (2105)         |
|PE-101     |2021.102.108.112, <br>         |OTA 或 USB        |[2021.105.111.112 OTA 清单 (PE-101)](https://go.microsoft.com/fwlink/?linkid=2155625)<br>[2021.105.111.112 OTA 更新包](https://go.microsoft.com/fwlink/?linkid=2161538)<br>[2021.105.111.112 USB 更新包](https://go.microsoft.com/fwlink/?linkid=2155734)          |5 月版本 (2105)         |
|APDK-101     |所有的 swVersion        |OTA 或 USB       | [2021.105.111.112 OTA 清单 (APDK-101)](https://go.microsoft.com/fwlink/?linkid=2163554)<br>[2021.105.111.112 OTA 更新包](https://go.microsoft.com/fwlink/?linkid=2163456)<br>[2021.105.111.112 USB 更新包](https://go.microsoft.com/fwlink/?linkid=2163555)        |5 月版本 (2105)         |


## <a name="next-steps"></a>后续步骤
通过上一节中确定的方法和更新包来更新开发工具包。
- [以无线方式更新 Azure Percept DK](./how-to-update-over-the-air.md)
- [通过 USB 更新 Azure Percept DK](./how-to-update-via-usb.md)