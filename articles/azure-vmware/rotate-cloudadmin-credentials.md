---
title: 为 Azure VMware 解决方案轮换 cloudadmin 凭据
description: 了解如何为 Azure VMware 解决方案私有云轮换 vCenter Server 凭据。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 22a88feb7e7b8656666d82cdac4b4d02d546441e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258064"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>为 Azure VMware 解决方案轮换 cloudadmin 凭据

>[!IMPORTANT]
>目前，不支持轮换 NSX-T Manager 管理员凭据。  若要轮换 NSX-T Manager 密码，请提交[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)。 此过程可能会影响 HCX 服务的运行。

在本文中，你将为 Azure VMware 解决方案私有云轮换 cloudadmin 凭据（vCenter Server CloudAdmin 凭据）。  尽管此帐户的密码不会过期，但随时都可以生成一个新密码。

>[!CAUTION]
>如果使用 cloudadmin 用户凭据将服务连接到私有云中的 vCenter，则轮换密码后，这些连接将立即中断。 除非在轮换密码之前停止这些服务，否则这些连接还会锁定 cloudadmin 帐户。

## <a name="prerequisites"></a>先决条件

在轮换密码之前，请考虑并确定以 cloudadmin@vsphere.local 身份连接到 vCenter 的服务。 这些服务可能包括 VMware 服务，例如 HCX、vRealize Orchestrator、vRealize Operations Manager、VMware Horizon 或其他用于监视或预配的第三方工具。 

确定通过 cloudadmin 用户对 vCenter 进行身份验证的服务的一种方式是使用私有云的 vSphere 客户端检查 vSphere 事件。 确定此类服务后，在轮换密码之前，必须停止这些服务。 否则，在轮换密码后，服务将无法正常运作。 你还会遇到 vCenter CloudAdmin 帐户出现临时锁定的情况，因为这些服务会持续尝试使用缓存的旧凭据版本进行身份验证。 

建议针对每项服务使用单独的帐户，而不是使用 cloudadmin 用户将服务连接到 vCenter。 有关为连接服务设置单独帐户的详细信息，请参阅[访问和标识的概念](./concepts-identity.md)。

## <a name="reset-your-vcenter-credentials"></a>重置 vCenter 凭据

1. 在 Azure 门户中，打开 Azure Cloud Shell 会话。

2. 更新 vCenter CloudAdmin 凭据。  请记得将 {SubscriptionID}、{ResourceGroup} 和 {PrivateCloudName} 替换为私有云信息  。 

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
 
## <a name="update-hcx-connector"></a>更新 HCX 连接器 

1. 转到本地 HCX 连接器（其地址为 https://{HCX 连接器设备的 IP}:443），然后使用新凭据登录。

   请务必使用端口 443。 

2. 在 VMware HCX 仪表板上，选择“站点配对”。
    
   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="VMware HCX 仪表板的屏幕截图，其中突出显示了“站点配对”。":::
 
3. 选择 Azure VMware 解决方案的正确连接，然后选择“编辑连接”。
 
4. 提供新的 vCenter 用户凭据，然后选择“编辑”，这会保存凭据。 应显示保存成功。


## <a name="next-steps"></a>后续步骤

了解如何重置 Azure VMware 解决方案的 vCenter 凭据后，接下来你可能想要了解以下操作：

- [在 Azure VMware 解决方案中集成 Azure 原生服务](integrate-azure-native-services.md)
- [使用 VMware HCX 为 Azure VMware 解决方案工作负载部署灾难恢复](deploy-disaster-recovery-using-vmware-hcx.md)
