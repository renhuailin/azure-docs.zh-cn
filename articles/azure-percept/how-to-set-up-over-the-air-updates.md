---
title: 设置 Azure IoT 中心以部署无线更新
description: 了解如何配置 Azure IoT 中心以通过无线方式将更新部署到 Azure Percept DK
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 26e53511cfab9444a9d5a9c4dcd868be07b40e16
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224353"
---
# <a name="set-up-azure-iot-hub-to-deploy-over-the-air-updates"></a>设置 Azure IoT 中心以部署无线更新

使用无线更新使 Azure Percept DK 保持安全和最新状态。 只需简单几步，即可使用 Device Update for IoT Hub 设置 Azure 环境，并将最新更新部署到 Azure Percept DK。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将开发工具包连接到 Wi-Fi 网络，创建了 IoT 中心，并已将开发工具包连接到 IoT 中心

## <a name="create-a-device-update-account"></a>创建设备更新帐户

1. 转到 [Azure 门户](https://portal.azure.com)，并使用用于 Azure Percept 的 Azure 帐户登录。

1. 在页面顶部的搜索栏中，输入“Device Update for IoT Hub”。

1. 当搜索栏中显示“Device Update for IoT Hub”时，选中该项。

1. 选择页面左上部分的“+ 添加”按钮。

1. 选择与你的 Azure Percept 设备及其 IoT 中心相关联的 Azure 订阅和资源组。 

1. 指定设备更新帐户的名称和位置。 

1. 选中显示“分配设备更新管理员角色”的框。 

1. 查看详细信息，并选择“查看 + 创建”。

1. 选择“创建”按钮。

1. 部署完成后，单击“转到资源”。

## <a name="create-a-device-update-instance"></a>创建设备更新实例

1. 在 Device Update for IoT Hub 资源中，单击“实例管理”下的“实例” 。

1. 单击“+ 创建”，指定实例名称，并选择与 Azure Percept 设备关联的 IoT 中心。 完成此命令可能需要几分钟。

1. 单击 **“创建”** 。

## <a name="configure-iot-hub"></a>配置 IoT 中心

1. 在实例管理“实例”页上，等待设备更新实例转变为“成功”状态 。 单击“刷新”图标更新状态。

1. 选择已为你创建的实例，并单击“配置 IoT 中心”。 在左窗格中，选择“我同意进行这些更改”，然后单击“更新” 。

1. 等待该过程成功完成。

## <a name="configure-access-control-roles"></a>配置访问控制角色

最后的步骤将使你能够向用户授予发布和部署更新的权限。

1. 在 Device Update for IoT Hub 资源中，单击“访问控制(IAM)”。

1. 单击“+ 添加”，然后选择“添加角色分配” 。

1. 对于“角色”，选择“设备更新管理员” 。 对于“将访问权限分配给”，请选择“用户、组或服务主体” 。 对于“选择”，选择你的帐户或将部署更新的人员的帐户。 单击“保存”。

> [!TIP]
> 如果你想为组织中的更多人员提供访问权限，则可以重复此步骤，并使所有这些用户都成为设备更新管理员。

## <a name="next-steps"></a>后续步骤

现在，你可以使用 Device Update for IoT Hub [以无线方式更新 Azure Percept 开发工具包](./how-to-update-over-the-air.md)。