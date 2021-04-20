---
title: 重置 Azure VMware 解决方案的 vSphere 凭据
description: 了解如何重置 Azure VMware 解决方案私有云的 vSphere 凭据，并确保 HCX 连接器具有最新的 vSphere 凭据。
ms.topic: how-to
ms.date: 03/31/2021
ms.openlocfilehash: 793b79e42a0adbca54804d1b66102736aff22d7a
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109095"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>重置 Azure VMware 解决方案的 vSphere 凭据

本文将介绍为 Azure VMware 解决方案私有云重置 vCenter Server 和 NSX-T Manager 凭据的步骤。 这可以让你确保 HCX 连接器具有最新的 vCenter Server 凭据。

除了查看本操作指南，还可以观看[重置 VCenter CloudAdmin 和 NSX-T 管理员密码](https://youtu.be/cK1qY3knj88)的视频。

## <a name="reset-your-azure-vmware-solution-credentials"></a>重置 Azure VMware 解决方案凭据

 首先，让我们重置 Azure VMare 解决方案组件凭据。 你的 vCenter Server CloudAdmin 和 NSX-T 管理员凭据不会过期；但是，你可以按照这些步骤为这些帐户生成新密码。

> [!NOTE]
> 如果将 CloudAdmin 凭据用于连接服务（如 HCX、vRealize Orchestrator、vRealizae Operations Manager 或 VMware Horizon），则更新密码后，连接会停止工作。  在启动密码轮换之前应停止这些服务。  否则可能会导致 vCenter CloudAdmin 和 NSX-T 管理员帐户临时锁定，因为这些服务将使用你的旧凭据继续调用。  有关为连接服务设置单独帐户的详细信息，请参阅[访问和标识的概念](https://docs.microsoft.com/azure/azure-vmware/concepts-identity)。

1. 在 Azure 门户中，打开 Azure Cloud Shell 会话。

2. 运行以下命令以更新你的 vCenter CloudAdmin 密码。  需要将 {SubscriptionID}、{ResourceGroup} 和 {PrivateCloudName} 替换为 CloudAdmin 帐户所属的私有云的实际值。

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. 运行以下命令以更新你的 NSX-T 管理员密码。 需要将 {SubscriptionID}、{ResourceGroup} 和 {PrivateCloudName} 替换为 NSX-T 管理员帐户所属的私有云的实际值。

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>确保 HCX 连接器具有最新的 vCenter Server 凭据

重置凭据后，按照以下步骤确保 HCX 连接器具有已更新的凭据。

1. 更改密码后，使用 https://{HCX 连接器设备的 IP}:443 转到本地 HCX 连接器 Web 界面。 请务必使用端口 443。 使用新凭据登录。

2. 在 VMware HCX 仪表板上，选择“站点配对”。
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="VMware HCX 仪表板的屏幕截图，其中突出显示了“站点配对”。":::
 
3. 选择与 Azure VMware 解决方案的正确连接（如果有多个），然后选择“编辑连接”。
 
4. 提供新的 vCenter Server CloudAdmin 用户凭据并选择“编辑”，这将保存凭据。 应显示保存成功。

## <a name="next-steps"></a>后续步骤

了解如何重置 Azure VMware 解决方案的 vCenter Server 和 NSX-T Manager 凭据后，接下来你可能需要了解以下操作：

- [在 Azure VMware 解决方案中配置 NSX 网络组件](configure-nsx-network-components-azure-portal.md)。
- [Azure VMware 解决方案 VM 的生命周期管理](lifecycle-management-of-azure-vmware-solution-vms.md)。
- [使用 Azure VMware 解决方案部署虚拟机的灾难恢复](disaster-recovery-for-virtual-machines.md)。
