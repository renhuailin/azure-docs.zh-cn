---
title: 管理订阅
description: 订阅由托管的已提交设备组成，可根据需要加入或移除。
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387126"
---
# <a name="manage-a-subscription"></a>管理订阅

系统每月管理一次订阅。 加入一项订阅后，系统将会针对该订阅向你收费，直至月底。 同理，如果移除订阅，系统将会根据移除订阅当月的剩余时间向你收费。

## <a name="onboard-a-subscription"></a>加入订阅

若要使用 Azure Defender for IoT，你必须先订阅 Microsoft Azure。 如果尚无订阅，可注册免费帐户。 如果已具有 Azure 订阅的访问权限，但未列出，请检查帐户详情，确认你拥有订阅所有者的相关权限。

加入订阅：

1. 导航到 Azure 门户的定价页。 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="导航到 Azure 门户的定价页。":::

1. 选择“加入订阅”。

1. 在“加入订阅”窗口中，选择订阅，并从下拉菜单中选择已提交设备数。 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="选择订阅和已提交设备数。":::

1. 选择“加入”。

## <a name="offboard-a-subscription"></a>移除订阅

系统每月管理一次订阅。 移除订阅时，系统将会针对该订阅向你收费，直至月底。

移除订阅之前，请卸载与该订阅关联的所有传感器。 有关如何删除传感器的详细信息，请参阅[删除传感器](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor)。 

移除订阅：

1. 导航至“定价”页。
1. 选择订阅，然后点击“删除”图标:::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false":::。
1. 在确认弹窗中，勾选相应复选框，以确认你已删除与该订阅关联的所有传感器。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="勾选相应复选框，然后点击“移除”按钮，以移除传感器。":::

1. 点击“移除”按钮。 

## <a name="next-steps"></a>后续步骤

[激活和设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)
