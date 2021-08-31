---
title: 管理订阅
description: 订阅由托管的已提交设备组成，可根据需要加入或移除。
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: b2d9c5ba3b359415f1dcfa5772003aec04a80855
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252486"
---
# <a name="manage-defender-for-iot-subscriptions"></a>管理 Defender for IoT 订阅

## <a name="about-subscriptions"></a>关于订阅

Defender for IoT 部署是通过 Azure Defender for IoT 帐户订阅进行管理的。
可以从 [Azure Defender for IoT 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)加入、编辑和移除 Defender for IoT 订阅。

对于每个订阅，都需要定义一个已提交设备数。 已提交设备数是要在企业中监视的大致设备数。 

### <a name="subscription-billing"></a>订阅计费

帐单是根据与每个订阅关联的已提交设备数计费的。

Azure Defender for IoT 的计费周期按日历月计算。 当月对已提交设备数所做的更改将在确认更新后的一小时内完成，并会反映在月度帐单中。 订阅移除也是在确认移除后的一小时内生效。

你的企业可能与多个支付实体合作。 如果是这种情况，你可以加入多个订阅。

在加入订阅之前，应了解希望订阅涵盖的设备数。

此外，用户还可以使用试用版订阅，它支持在 30 天内监视有限数量的设备。
请参阅 [Azure Defender 定价](https://azure.microsoft.com/pricing/details/azure-defender/)，了解已提交设备价格的信息。

### <a name="before-you-begin"></a>在开始之前

在加入订阅之前，请确认：
- 已设置 Azure 帐户。
- 拥有所需的 Azure 用户权限。
#### <a name="azure-account-setup"></a>Azure 帐户设置

若要使用 Azure Defender for IoT，你必须先订阅 Microsoft Azure。 如果尚无订阅，可注册免费帐户。 如果已具有对 Azure 订阅的访问权限，但在订阅时未列出，请查看帐户详情，确认拥有订阅所有者的相关权限。

- 如果已有帐户： https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade 。

- 如果还没有帐户： https://azure.microsoft.com/free/ 。

#### <a name="user-permission-requirements"></a>用户权限要求

Azure 订阅所有者和订阅参与者可以加入、更新和移除 Azure Defender for IoT 订阅。

## <a name="onboard-a-trial-subscription"></a>加入试用版订阅

若要评估 Defender for IoT，可以使用试用版订阅。 试用版的有效期为 30 天，支持 1000 台已提交设备。 使用试用版，可以在网络中再部署一个 Defender for IoT 传感器。 使用传感器可以监视流量、分析数据、生成警报、了解网络风险和漏洞等。 使用试用版，还可以下载一个本地管理控制台，用来查看传感器生成的汇总信息。

本部分介绍如何为传感器创建试用版订阅。

**创建试用版订阅：**

1. 导航到 [Azure Defender for IoT 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)。
1. 选择“开始使用”。
1. 选择“加入订阅”。
1. 在“定价”页中，选择“开始使用试用版”。
1. 从“加入试用版订阅”窗格中选择一个订阅，然后选择“评估”。
1. 确认评估。
1. 根据需要加入传感器或设置传感器。

## <a name="onboard-a-subscription"></a>加入订阅

本部分介绍如何加入订阅。

**加入订阅：**

1. 导航到 [Azure Defender for IoT 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)。
1. 选择“开始使用”。
1. 选择“加入订阅”。
1. 在“定价”页中，选择“订阅”。
1. 在“加入订阅”窗格中，选择一个订阅，并从下拉菜单中选择已提交设备数。

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="选择订阅和已提交设备数。":::

1. 选择“订阅”。
1. 确认订阅。
1. 如果你还没有执行此操作，请加入传感器或设置一个传感器。

## <a name="update-committed-devices-in-a-subscription"></a>更新订阅中的已提交设备数

你可能需要使用更多的已提交设备，或更少的已提交设备来更新你的订阅。 可能需要监视更多设备，例如，你正在增加现有站点覆盖范围、发现的设备数超过预期或者存在网络更改（例如添加交换机）。

**更新订阅：**
1. 导航到 [Azure Defender for IoT 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)。
1. 选择“加入订阅”。
1. 选择订阅，然后选择三个点。 (...).
1. 选择“编辑”。
1. 更新已提交设备数，然后选择“保存”。
2. 在打开的确认对话框中，选择“确认”。
设备提交的更改将在确认更改后的一小时内生效。 针对这些更改的计费将在确认更改后的月初反映出来。

你需要将新的激活文件上传到本地管理控制台。 激活文件反映了新的已提交设备数。 请参阅[上传激活文件](how-to-manage-the-on-premises-management-console.md#upload-an-activation-file)。
## <a name="offboard-a-subscription"></a>移除订阅

你可能需要移除订阅，例如，在需要与新的支付实体合作的情况下。 订阅移除将在确认移除后的一小时内生效。
你即将收到的月度帐单将反映这一变更。

在移除订阅之前，请先删除与该订阅关联的所有传感器。 有关如何删除传感器的详细信息，请参阅[删除传感器](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor)。

**移除订阅：**

1. 导航到 [Azure Defender for IoT 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)。
1. 选择订阅，然后选择三个点。 (...).

1. 选择“移除订阅”。

1. 在确认弹窗中，勾选相应复选框，以确认你已删除与该订阅关联的所有传感器。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="勾选相应复选框，然后点击“移除”按钮，以移除传感器。":::

1. 选择“移除”。

## <a name="apply-a-new-subscription"></a>应用新订阅

出于业务考虑，你可能需要对部署应用一个与目前使用的不同的订阅。 如果要更改订阅，需要上传一个新的传感器激活文件。 该文件包含有关订阅到期日期的信息。

**应用新订阅：**

1. 删除目前使用的订阅。
1. 选择一个新订阅。
1. 为与订阅关联的传感器下载一个激活文件。
1. 将激活文件上传到传感器。

## <a name="next-steps"></a>后续步骤

- [在 Defender for IoT 门户中管理传感器](how-to-manage-sensors-on-the-cloud.md)

- [激活和设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)
