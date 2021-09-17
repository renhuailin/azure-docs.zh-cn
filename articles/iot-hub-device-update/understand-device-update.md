---
title: Device Update for Azure IoT Hub 简介 | Microsoft Docs
description: Device Update for IoT Hub 是一项服务，可用于为 IoT 设备部署无线更新 (OTA)。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 3a7ed9fbb30ab770cd7069ebbd97faa41b12cd34
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606833"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Device Update for IoT Hub（预览版）概述

Device Update for IoT Hub 是一项服务，可用于为 IoT 设备部署无线更新 (OTA)。

随着组织寻求进一步提高工作效率和运营效率，物联网 (IoT) 解决方案的采用率持续上升。 这使得生成这些解决方案的设备必须建立在可靠性和安全性的基础上，并且易于大规模连接和管理。 Device Update for IoT Hub 是一个端到端平台，客户可以使用它来发布、分发和管理从微型传感器到网关级设备的所有内容的无线更新。 

为了实现 IoT 支持的数字化转型的全部优势，客户需要这种能够大规模操作、维护和更新设备的功能。 探索实现 Device Update for IoT Hub 的好处，其中包括能够快速响应安全威胁和部署新功能以实现业务目标，在构建自己的更新平台时不会产生额外的开发和维护成本。

## <a name="support-for-a-wide-range-of-iot-devices"></a>支持各种 IoT 设备


Device Update for IoT Hub 旨在通过与 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)集成，提供优化的更新部署和简化的操作。 通过这种集成，可以轻松地在任何现有解决方案中采用 Device Update。 它提供了可用于连接几乎任何设备的云托管解决方案。 Device Update 支持范围广泛的 IoT 操作系统，包括 Linux 和 [Azure RTOS](https://azure.microsoft.com/services/rtos/)（实时操作系统），并且可通过开放源代码进行扩展。 我们正在与半导体合作伙伴（包括 STMicroelectronics、NXP、Renesas 和 Microchip）共同开发 IoT 中心产品/服务的设备更新。 请参阅主要半导体评估版块的[示例](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)（包括入门指南），以了解如何配置、生成和部署 MCU 类设备的无线 (OTA) 更新。 

同时提供了 Device Update 代理模拟器二进制文件和 Raspberry Pi 引用 Yocto 映像。
Device Update for IoT Hub 还支持更新 Azure IoT Edge 设备。 为 Ubuntu Server 18.04 amd64 平台提供了 Device Update 代理。 如果你运行的不是上述任一平台，Device Update for IoT Hub 还会提供开源代码。 你可以将代理移植到你正在运行的分发版。

Device Update 适用于 IoT 即插即用，可以管理支持所需 IoT 即插即用接口的任何设备。 有关详细信息，请参阅 [Device Update for IoT Hub 与 IoT 即插即用](device-update-plug-and-play.md)。

## <a name="support-for-a-wide-range-of-update-artifacts"></a>支持各种更新项目

Device Update for IoT Hub 支持两种形式的更新：基于映像的更新和基于包的更新。

基于包的更新是有针对性的更新，只改变设备上的特定组件或应用程序。 这将降低带宽的消耗，并有助于减少下载和安装更新的时间。 包更新通常可以在应用更新时减少设备的停机时间，并避免创建图像的开销。

映像更新在设备的最终状态中提供更高级别的置信度。 通常可以更轻松地在预生产环境和生产环境之间复制映像更新的结果，因为它不会带来与包及其依赖项相同的挑战。
由于其原子性质，还可以轻松采用 A/B 故障转移模型。

没有一个正确答案，你可以根据具体的用例做出不同的选择。 Device Update for IoT Hub 支持映像和包形式的更新，使你可以为设备环境选择正确的更新模型。

## <a name="flexible-features-for-updating-devices"></a>用于更新设备的灵活功能

Device Update for IoT Hub 功能提供了功能强大且灵活的体验，其中包括：

* 与 Azure IoT 中心集成的更新管理 UX
* 通过设备分组和更新计划控件逐步推出更新
* 用于实现自动化和自定义门户体验的编程 API
* 异质设备群中一目了然的更新合规性和状态视图
* 支持弹性设备更新 (A/B) 以提供无缝回滚
* 通过 Azure.com 门户提供订阅和基于角色的访问控制
* 支持本地内容缓存和嵌套边缘，以便能够更新云端离线设备
* 详细的更新管理和报告工具 

借助 Device Update for IoT Hub 管理和部署控件，用户可以最大程度地提高工作效率并节省宝贵的时间。 使用 Device Update for IoT Hub，可以对设备分组，并指定应将更新部署到的设备。 用户还可以查看更新部署的状态，并确保每个设备都能成功应用更新。

发生更新失败时，Device Update for IoT Hub 还能让用户确定无法应用更新的设备，并查看相关的失败详细信息。 有了确定哪些设备无法更新的功能，不必再花费大量时间来尝试手动查明原因。

### <a name="best-in-class-security-at-global-scale"></a>全球范围内的最佳安全级别

Microsoft Azure 支持全球超过 10 亿台 IoT 设备，这个数字每天都在快速增长。 Device Update for IoT Hub 建立在这一体验和 Windows 更新平台证明的可靠性基础上，因此设备可以在全球范围内无缝更新。

Device Update for IoT Hub 使用为 Microsoft Azure 开发的全面的云到边缘安全性，因此客户无需花费时间来确定如何从头开始构建它。


## <a name="device-update-workflows"></a>Device Update 工作流

Device Update 功能可分为三个部分：代理集成、导入和管理。

### <a name="device-update-agent"></a>Device Update 代理

当设备上收到更新命令时，它将执行请求的更新阶段（下载、安装和应用）。 在每个阶段中，状态通过 IoT 中心返回到 Device Update，便于你查看部署的当前状态。 如果没有正在进行的更新，则返回“空闲”状态。 可以随时取消部署。

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Device Update 代理工作流的关系图。" lightbox="media/understand-device-update/client-agent-workflow.png":::

[详细了解](device-update-agent-overview.md) Device Update 代理。 

### <a name="importing"></a>导入

导入是将更新引入到“设备更新”中的方式，目的是将其部署到设备。 Device Update 支持为每个设备推出一个更新。 这使它非常适合于一次更新整个 OS 分区，或者是描述你要在设备上更新的所有包的 apt 清单的完整映像更新。 若要将更新导入到 Device Update 中，请先创建一个描述更新的导入清单，然后将更新文件和导入清单上传到可访问 Internet 的位置。 之后，你可以使用 Azure 门户或[设备更新 REST API](/rest/api/deviceupdate/) 启动更新导入的异步过程。 Device Update 上传文件、处理文件，并使它们可分发到 IoT 设备。

对于敏感内容，请使用共享访问签名 (SAS)（如 Azure Blob 存储的临时 SAS）来保护下载内容。 [详细了解 SAS](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Device Update for IoT Hub 导入工作流关系图。" lightbox="media/understand-device-update/import-update.png":::

[详细了解](import-concepts.md)如何导入更新。 

### <a name="grouping-and-deployment"></a>分组和部署

导入更新后，你可以查看设备和设备类的兼容更新。

Device Update 通过 IoT 中心中的标记支持组的概念。 首先将更新部署到测试组是降低生产推出期间问题风险的好方法。

在 Device Update 中，部署是将正确内容连接到一组特定的兼容设备的方法。 Device Update 会协调将命令发送到每台设备的过程，指示它们下载和安装更新并获取返回的状态。

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Device Update for IoT Hub 分组和部署工作流关系图。" lightbox="media/understand-device-update/manage-deploy-updates.png":::

[详细了解](device-update-compliance.md)部署的概念

[详细了解](device-update-groups.md)设备更新组


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建设备更新帐户和实例](create-device-update-account.md)
