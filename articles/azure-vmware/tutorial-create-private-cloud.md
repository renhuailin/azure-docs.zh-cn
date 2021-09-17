---
title: 教程 - 部署 Azure VMware 解决方案私有云
description: 了解如何创建和部署 Azure VMware 解决方案私有云
ms.topic: tutorial
ms.date: 06/11/2021
ms.openlocfilehash: d91e9fe9261aa4a04f5e5dffd3505742d9886623
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730349"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>教程：部署 Azure VMware 解决方案私有云

通过使用 Azure VMware 解决方案私有云，可以在 Azure 中部署 vSphere 群集。 默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可添加、删除和缩放群集。  每个群集的最小主机数目为 3 个。 可一次添加更多主机，每个群集最多可以包含 16 个主机。 每个私有云的最大群集数目为 4 个。  Azure VMware 解决方案的初始部署有 3 台主机。 

试用群集可用于评估并限制为 3 台主机。 每个私有云有一个试用群集。 可以在评估期内按单个主机缩放试用群集。

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。

由于 Azure VMware 解决方案不允许在启动时使用本地 vCenter 管理私有云，因此需要执行额外的配置步骤。  本教程介绍了这些步骤和相关的先决条件。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证已部署的私有云

## <a name="prerequisites"></a>先决条件

- 拥有适当的管理权限和创建私有云的权限。 你在订阅中必须至少是参与者级别。
- 按照在[规划](plan-private-cloud-deployment.md)教程中收集的信息部署 Azure VMware 解决方案。
- 确保已按照[网络规划清单](tutorial-network-checklist.md)中的说明配置相应的网络。
- 主机已预配，Microsoft.AVS [资源提供程序已注册](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)。

## <a name="create-a-private-cloud"></a>创建私有云

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证已部署的私有云
> * 删除 Azure VMware 解决方案私有云

继续学习下一教程，了解如何创建跳转盒。 使用跳转盒连接到你的环境以在本地管理你的私有云。


> [!div class="nextstepaction"]
> [访问 Azure VMware 解决方案私有云](tutorial-access-private-cloud.md)
