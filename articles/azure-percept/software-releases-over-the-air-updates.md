---
title: Azure Percept DK OTA 更新的软件版本
description: Azure Percept DK 无线更新包的信息和下载链接
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: c8f05b1c2d04e492b44aef88ac31a8e4dece5e16
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222861"
---
# <a name="software-releases-for-ota-updates"></a>OTA 更新的软件版本

OTA 更新是专为那些倾向于始终使开发工具包保持最新的用户而构建的。 这就是为什么本文只提供了必装版本和最新版本。 若要将开发工具包更改为特定（较旧）版本，请使用 USB 数据线更新。 请参阅[通过 USB-C 数据线连接更新 Azure Percept DK](./how-to-update-via-usb.md)。 如果要跳至更高级的版本，也可以使用 USB 更新。

>[!CAUTION]
>开发工具包不支持通过 OTA 降级 SW 版本。 Device Update for IoT Hub 框架不会阻止部署比当前版本更低的映像。 但是，在开发工具包中进行这种部署会导致数据和功能丢失。

>[!IMPORTANT]
>在决定采用 OTA 或 USB 数据线更新之前，请务必查看以下文档。
>
>[如何确定更新策略](./how-to-determine-your-update-strategy.md)

## <a name="hard-stop-version-of-ota"></a>OTA 的必装版本

Microsoft 将为每个包含 OTA 包的开发工具包版本提供服务。 但是，由于开发工具包 OS/固件或 OTA 平台发生中断性变更，因此，直接从旧版本通过 OTA 更新到更高级版本可能会出现问题。 通常，在发生中断性变更时，Microsoft 会确保 OTA 更新过程将旧系统无缝过渡到引入/传递此中断性变更的第一个版本。 这个特定的版本将成为 OTA 的必装版本。 以一个已知的必装版本：6 月版本为例。 如果用户将开发工具包从 2104 更新到 2106，再从 2106 更新到 2107，则 OTA 将正常工作。 但是，如果用户尝试跳过必装版本 (2106)，并将开发工具包从 2104 直接升级到 2107，它将无法正常工作。

:::image type="content" source="./media/azure-percept-devkit-software-releases-ota-update/hard-stop-illustrate.png" alt-text="OTA 的必装版本":::

## <a name="recommendations-for-applying-the-ota-update"></a>应用 OTA 更新的建议

方案 1：按一定的频率（每月）更新开发工具包，确保它始终是最新的

- 如果你始终采用 OTA 将开发工具包从上一版本更新到新发布的版本，应该没有问题。

方案 2：进行更新时可能会跳过几个版本。

1. 标识开发工具包的当前软件版本。
1. 查看 OTA 包版本列表，看看当前版本和目标版本之间是否有任何必装版本。
    - 如果有，则需要按顺序部署必装版本，直到可以部署最新的更新包。
    - 如果没有，可以直接将最新的 OTA 包部署到开发工具包。

## <a name="identify-the-current-software-version-of-dev-kit"></a>标识开发工具包的当前软件版本

选项 1：

1. 登录到 [Azure Percept Studio](./overview-azure-percept-studio.md)。
1. 在“设备”中，选择你的开发工具包设备。
1. 在“常规”选项卡中，查找“模型”和“软件版本”信息。  

选项 2：

1. 从 Microsoft Azure 门户查看 IoT 中心服务的 IoT 边缘设备。
1. 从设备列表中选择开发工具包设备。
1. 选择“设备孪生”。
1. 滚动浏览设备孪生属性，在“deviceInformation”下面找到“model”和“swVersion”并记下其值。  

## <a name="identify-the-ota-packages-to-be-deployed"></a>标识要部署的 OTA 包

>[!IMPORTANT]
>如果以下任何版本中均未包含你的开发工具包的当前版本，表明当前版本不支持进行 OTA 更新。 请进行 USB 数据线更新以更新到最新版本。

最新版本：

|发布|适用版本|下载链接|注意|
|---|---|---|---|
|7 月服务版本 (2107)|2021.106.111.115|[2021.107.129.116 OTA 更新包](https://go.microsoft.com/fwlink/?linkid=2169245)||

必装版本：

|发布|适用版本|下载链接|注意|
|---|---|---|---|
|6 月服务版本 (2106)|2021.102.108.112、2021.104.110.103、2021.105.111.122 |[2021.106.111.115 OTA 清单（对于 PE-101）](https://go.microsoft.com/fwlink/?linkid=2167127)<br>[2021.106.111.115 OTA 清单（对于 APDK-101）](https://go.microsoft.com/fwlink/?linkid=2167235) <br>[2021.106.111.115 OTA 更新包](https://go.microsoft.com/fwlink/?linkid=2167128) |请确保根据“模型名称”(PE-101/APDK-101) 使用正确的清单|

## <a name="next-steps"></a>后续步骤

[以无线方式 (OTA) 更新 Azure Percept DK](./how-to-update-over-the-air.md)
