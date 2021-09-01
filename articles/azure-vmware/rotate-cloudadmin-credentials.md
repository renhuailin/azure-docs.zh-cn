---
title: 为 Azure VMware 解决方案轮换 cloudadmin 凭据
description: 了解如何为 Azure VMware 解决方案私有云轮换 vCenter Server 和 NSX-T Manager 凭据。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 884e3f09e429fff39c0dfafcb5d0c9a7b4915e03
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324775"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>为 Azure VMware 解决方案轮换 cloudadmin 凭据

在本文中，你会为 Azure VMware 解决方案私有云轮换 cloudadmin 凭据（vCenter 和 NSX-T 凭据）。  尽管这些帐户的密码不会过期，但你可以生成新密码。 生成新密码后，必须在应用最新凭据的情况下更新 VMware HCX 连接器。

还可以观看有关如何[重置 vCenter CloudAdmin 和 NSX-T 管理员密码](https://youtu.be/cK1qY3knj88)的视频。 

## <a name="prerequisites"></a>先决条件

如果将 cloudadmin 凭据用于连接服务（如 HCX、vRealize Orchestrator、vRealize Operations Manager 或 VMware Horizon），你的连接会在更新密码后停止工作。  因此请在启动密码轮换之前先停止这些服务。 否则会出现 vCenter CloudAdmin 和 NSX-T 管理员帐户临时锁定的情况，因为这些服务会使用旧凭据继续进行调用。  有关为连接服务设置单独帐户的详细信息，请参阅[访问和标识的概念](./concepts-identity.md)。

## <a name="reset-your-azure-vmware-solution-cloudadmin-credentials"></a>重置 Azure VMware 解决方案 cloudadmin 凭据

在这一步中，你将轮换 Azure VMware 解决方案组件的 cloudadmin 凭据。 

>[!NOTE]
>请记得将 {SubscriptionID}、{ResourceGroup} 和 {PrivateCloudName} 替换为私有云信息  。

1. 在 Azure 门户中，打开 Azure Cloud Shell 会话。

2. 更新 vCenter CloudAdmin 密码。  

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
          
3. 更新 NSX-T 管理员密码。 

   ```azurecli-interactive
   az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

## <a name="update-hcx-connector-with-the-latest-cloudadmin-credentials"></a>使用最新 cloudadmin 凭据更新 HCX 连接器

在此步骤中，你会使用更新的凭据更新 HCX 连接器。

1. 转到本地 HCX 连接器（其地址为 https://{HCX 连接器设备的 IP}:443），然后使用新凭据登录。

   请务必使用端口 443。 

2. 在 VMware HCX 仪表板上，选择“站点配对”。
    
   :::image type="content" source="media/rotate-cloudadmin-credentials/hcx-site-pairing.png" alt-text="VMware HCX 仪表板的屏幕截图，其中突出显示了“站点配对”。":::
 
3. 选择 Azure VMware 解决方案的正确连接，然后选择“编辑连接”。
 
4. 提供新的 vCenter Server CloudAdmin 用户凭据并选择“编辑”，这将保存凭据。 应显示保存成功。

## <a name="next-steps"></a>后续步骤

了解如何重置 Azure VMware 解决方案的 vCenter Server 和 NSX-T Manager 凭据后，接下来你可能需要了解以下操作：

- [在 Azure VMware 解决方案中集成 Azure 原生服务](integrate-azure-native-services.md)
- [使用 VMware HCX 为 Azure VMware 解决方案工作负载部署灾难恢复](deploy-disaster-recovery-using-vmware-hcx.md)
